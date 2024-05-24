# 재고시스템으로 알아보는 동시성이슈 해결방법

[재고시스템으로 알아보는 동시성이슈 해결방법 | 최상용 - 인프런](https://www.inflearn.com/course/동시성이슈-재고시스템/dashboard)

재고를 감소 시키는 서비스

```java
@Service
@RequiredArgsConstructor
public class StockService {

    private final StockRepository stockRepository;

    public synchronized void decrease(Long id, Long quantity) {
        final Stock stock = stockRepository.findById(id).orElseThrow();
        stock.decrease(quantity);

        stockRepository.saveAndFlush(stock);
    }
}
```

이 간단한 코드를 multi-thread로 decrease 호출시키면 동시성 이슈가 발생한다.
(동일 id row에 대한 race-condition)

---

## 어플리케이션 레벨 락

### **1. Java synchronized**

```java
public synchronized void decrease(Long id, Long quantity) {
    final Stock stock = stockRepository.findById(id).orElseThrow();
    stock.decrease(quantity);

    stockRepository.saveAndFlush(stock);
}
```

- java synchronized 이제 decrease 메서드는 한 스레드만 접근을 할 수 있게 된다.
- 어플리케이션 레벨이기 때문에 여러 인스턴스(서버) 접근 시 race-condition이 또 발생한다.

### **2. @Transactional**

- Class나 method를 @Transactional로 선언하면 스프링의 AOP가 메서드콜을 PROXY로 감싸고, 이 PROXY는 트랜잭션 라이프 사이클에서 관리된다.
- 간단 코드로 이 감싸는 과정을 보면 다음과 같다.

```java
 public void decreases(Long id, Long quantity) {
     startTransaction();

     stockService.decreases(id, quantity);

     endTransaction();
 }
```

참고: @Transactional의 작동 원리 : 

[@Transactional 과 PROXY](https://velog.io/@chullll/Transactional-과-PROXY)

---

## **데이터베이스 락**

### **1. Pessimistic Lock**

- 데이터베이스 내 레코드에 대해 직접 락을 거는 것. (table, row)
- 정합성 측면에서는 가장 확실한 방법
- exclusive lock, shared lock
- deadlock 발생 할 수 있음

코드

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("select s from Stock s where s.id = :id")
Stock findByIdWithPessimisticLock(@Param("id") Long id);
```

- LockModeType으로 비관적락 설정

```sql
SELECT * FROM stock WHERE id = ? FOR UPDATE;
```

- 실제 실행 되는 쿼리는 이렇게 생겼을 것. (mysql 기준)

### **2. Optimistic Lock**

- 데이터베이스의 레코드에 직접 락을 생성하는 것이 아니라, 버전으로 race-condtion 관리
- read intensive한 경우 좋다.
- 데이터베이스에 락이 안걸리기 때문에 성능이 빠르다.
- fail case에 대한 코드 개발자가 직접 작성해야됨

코드

```java
@Lock(value = LockModeType.OPTIMISTIC)
@Query("select s from Stock s where s.id = :id")
Stock findByIdWithOptimisticLock(@Param("id") Long id);
```

```java
@Component
@RequiredArgsConstructor
public class OptimisticLockStockFacade {

    private final OptimisticLockStockService optimisticLockStockService;

    public void decrease(Long id, Long quantity) throws InterruptedException {
        while (true) {
            try {
                optimisticLockStockService.decrease(id, quantity);
                break;
            } catch (Exception e) {
                Thread.sleep(50);
            }
        }
    }
}
```

### **3. Named Lock**

- 메타데이터를 생성해서 실제 데이터 레코드가 아닌 메타데이터에 대한 락으로 제어
- 자동으로 락이 해제되지 않아서 개발자가 직접 수행
- 커넥션 풀 문제 발생 가능

```java
@Query(value = "select get_lock(:key, 3000)", nativeQuery = true)
void getLock(@Param("key") String key);

@Query(value = "select release_lock(:key)", nativeQuery = true)
void releaseLock(@Param("key") String key);
```

```java
@Component
@RequiredArgsConstructor
public class NamedLockStockFacade {

    private final LockRepository lockRepository;

    private final StockService stockService;

    @Transactional
    public void decrease(Long id, Long quantity) {
        try {
            lockRepository.getLock(id.toString());
            stockService.decrease(id, quantity);
        } finally {
            lockRepository.releaseLock(id.toString());
        }
    }
```

---

## **REDIS**

### **1. Lettuce**

- spin lock
- 코드가 단순함
- 트래픽 많을 시 계속 대기 상태로 레디스 부하

```java
public Boolean lock(Long key) {
    return redisTemplate
            .opsForValue()
            .setIfAbsent(generateKey(key), "lock", Duration.ofMillis(3_000));
}

public Boolean unlock(Long key) {
    return redisTemplate.delete(generateKey(key));
}
```

```java
public void decrease(Long key, Long quantity) throws InterruptedException {
    while (!redisLockRepository.lock(key)) {
        Thread.sleep(100);
    }

    try {
        stockService.decrease(key, quantity);
    } finally {
        redisLockRepository.unlock(key);
    }
}
```

### 2. Redisson

- pub-sub 메세징 방식
- spin lock 처럼 계속 확인하는 방식이 아니라서 부하가 덜 하다.

```java
public void decrease(Long key, Long quantity) {
    RLock lock = redissonClient.getLock(key.toString());

    try {
        boolean available = lock.tryLock(10, 1, TimeUnit.SECONDS);

        if (!available) {
            System.out.println("cannot obtain lock");
            return;
        }

        stockService.decrease(key, quantity);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    } finally {
        lock.unlock();
    }
}
```