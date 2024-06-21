# Thread and Lock

# 자바의 스레드

> 자바의 모든 스레드는 `java.lang.Thread` 클래스 객체에 의해 생성되고 제어된다.

독립적인 응용 프로그램이 실행될 때, main() 메서드를 실행하기 위한 하나의 user thread 가 자동으로 만들어지는데, 이 스레드를 main thread 라고 부른다.

자바에서 스레드를 구현하는 방법으로는 두 가지가 있다.

- `java.lang.Runnable` 인터페이스 구현하기
- `java.lang.Thread` 클래스를 상속받기

### java.lang.Runnable 인터페이스 구현하기

```java
public interface Runnable {
    void run();
}
```

Runnable 인터페이스를 사용해 스레드를 만들고 사용하려면 다음의 과정을 거쳐야 한다.

- Runnable 인터페이스를 구현하는 클래스를 만든다. 이 클래스의 객체는 Runnable 객체가 된다.
- Thread 타입의 객체를 만들 때, Thread 의 생성자에 Runnable 객체를 인자로 넘긴다. 이 Thread 객체는 이제 run() 메서드를 구현하는 Runnable 객체를 소유하게 된다.
- 이전 단계에서 생성한 Thread 객체의 start() 메서드를 호출한다.

```java
public class RunnableThreadExample implements Runnable {
    public int count = 0;
    
    // run() 메서드 구현
    public void run() {
		    // RunnableTread Starting
				try {
						while (count < 5) {
							Thread.sleep(500);
							count++;
						} 
				} catch (InterruptedException exc) {
					...
				}
		    // RunnableTread termination
    }
}

public static void main(String[] args) {
		RunnableTreadExample instance = RunnableTreadExample();
		Thread thread = new Thread(instance);
		thread.start();
		
		while (instance.count != 5) {
				try {
						Thread.sleep(250);
				} catch (InterruptedException exc) {
						...
				}
		}
}
```


### Thread 클래스 상속

> Thread 클래스를 상속받아서 스레드를 만들 수도 있다.
> 
- 항상 run() 메서드를 오버라이드해야 하며, 하위 클래스의 생성자는 상위 클래스의 생성자를 명시적으로 호출해야 한다.

```java
public class ThreadExample extends Thread {
	int count = 0;
	
	// run() override
	public void run() {
		// Thread starting.
		try {
			while (count < 5) {
				Thread.sleep(500);
				count++;
			}
		} catch (InterruptedException exc) {
			...
		}
		// Thread terminating.
	}
}

public class ExampleB {
	public static void main(String args[]) {
		ThreadExample instance = new ThreadExample();
		instance.start();
		
		while (instance != 5) {
			try {
				Thread.sleep(250);
   		} catch (InterruptedException exc) {
	   		exc.printStackTrace();
   		}
		}
	}
}
```

### Thread 상속 vs Runnable 인터페이스 구현

> 스레드 생성 시 Runnable 인터페이스를 구현하는 것이 Thread를 상속받는 것 보다 선호되는 이유
> 
- 자바는 다중 상속을 지원하지 않는다.
    - **Thread 상속 시** 하위 클래스는 다른 클래스를 상속할 수 없다.
    - **Runnable 인터페이스** 구현 시 다른 클래스를 상속할 수 있다.
- Thread 클래스의 모든 것을 상속받는 것이 너무 부담되는 경우 Runnable을 구현하는 편이 나을 수도 있다.


# 동기화와 락

> 자바는 공유 자원에 대한 접근을 제어하기 위한 동기화 방법을 제공한다.
> 
- 스레드가 서로 데이터를 공유할 수 있다는 점은 장점이긴 하지만, 두 스레드가 같은 자원을 동시에 변경하는 경우 문제가 될 수 있다.
- `synchronized` 와 `Lock` 키워드는 동기화 구현을 위한 기본이 된다.

### 동기화된 메서드

> 통상적으로 `synchronized` 키워드를 사용할 때는 공유 자원에 대한 접근을 제어한다.
> 
- **메서드**, **특정 코드 불록**에 적용할 수 있다.
- 여러 스레드가 같은 객체를 동시에 실행하는 것 또한 방지해준다.

```java
public class MyClass extends Thread {
	private String name;
	private MyObject myObj;
	
	public MyClass(MyObject obj, String n) {
		name = n;
		myObj = obj;
	}
	
	public void run() {
		myObj.foo(name);
	}
}

public class MyObject {
	public synchronized void foo(String name) {
		try {
			// Thread starting..
			Thread.sleep(3000);
			// Thread ending..
		} catch (InterruptedException exc) {
		// Thread Interrupted..
		}
	}	
}
```

- 서로 다른 객체인 경우 동시에 MyObject.foo() 호출이 가능하다.
- 같은 Obj를 가리키고 있는 경우 하나만 foo()를 호출할 수 있고, 다른 하나는 기다리고 있어야 한다.

정적 메서드는 `클래스 락`에 의해 동기화된다.

- 같은 클래스에 있는 동기화된 정적 메서드는 두 스레드에서 동시에 실행될 수 없다.

```java
public class MyClass extends Thread {
	...
	public void run() {
		if (name.equals("1") {
			MyObject.foo(name);
		} else if (name.equals("2")) {
			MyObject.bar(name);
		}
	}
}

public class MyObject {
	public static synchronized void foo(String name) { .. }
	public static synchronized void bar(String name) { .. }
}
```

### 동기화된 블록

> 특정한 **코드 블록**을 동기화할 수도 있다.
> 
- 메서드를 동기화하는 것과 아주 비슷하게 동작한다.

```java
public class MyClass extends Thread {
	...
	public void run() {
		myObj.foo(name);
	}
}

public class MyObject {
	public void foo(String name) {
		synchronized(this) {
			...
		}
	}
}
```

- 메서드를 동기화하는 것과 마찬가지로,
- MyObject 인스턴스 하나당 하나의 스레드만이 synchronized 블록 안의 코드를 실행할 수 있다.


### 락(Lock)

> 더 세밀하게 동기화를 제어하고 싶을 때는 락을 사용한다.
> 
- 락을 공유 자원에 붙이면 해당 자원에 대한 접근을 동기화할 수 있다.
- 스레드가 해당 자원을 접근하려면 우선 그 자원에 붙어 있는 **락을 획득**해야 한다.
- **특정 시간에 락을 쥐고 있을 수 있는 스레드는 하나뿐**이다.
    - 따라서 해당 공유자원은 한 번에 한 스레드만이 사용할 수 있다.
- 어떤 자원이 프로그램 내의 이곳저곳에서 사용되지만 한 번에 한 스레드만 사용하도록 만들고자 할 때 주로 락을 이용한다.

```java
public class LockedATM {
    private Lock lock;
    private int balance = 100;

    public LockedATM() {
        lock = new ReentrantLock();
    }

    public int withdraw(int value) {
        lock.lock();
        int temp = balance;
        try {
            temp = temp - value;
            balance = temp;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        lock.unlock();
        return temp;
    }

    public int deposit(int value) {
        lock.lock();
        int temp = balance;
        try {
            System.out.println("입금 시작");            
            temp = temp + value;
            
            System.out.println("입금 완료");
            balance = temp;
            
            System.out.println("현재 잔고 : "+ balance);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        lock.unlock();
        return temp;
    }

    public int balance() {
        return this.balance;
    }
```


# 교착상태와 교착상태 방지

> 교착상태(deadlock)
첫 번째 스레드는 두 번째 스레드가 들고 있는 개체의 락이 풀리기를 기다리고 있고,
두 번째 스레드 역시 첫 번째 스레드가 들고 있는 객체의 락이 풀리기를 기다리는 상황
> 
- 모든 스레드가 락이 풀리기를 기다리고 있기 때문에, 무한 대기 상태에 빠지게 된다.
- 이런 스레드를 교착 상태에 빠졌다고 한다.

교착상태가 발생하려면, 네 가지 조건이 모두 충족되어야 한다.

(1) 상호 배제(mutual exclusion)

- 한 번에 한 프로세스만 공유 자원을 사용할 수 있다.

(2) 들고 기다리기(hold and wait)

- 공유 자원에 대한 접근 제한을 가지고 있는 프로세스가, 그 접근 권한을 양보하지 않은 상태에서 다른 자원에 대한 접근 권한을 요구할 수 있다.

(3) 선취(preemption) 불가

- 한 프로세스가 다른 프로세스의 자원 접근 권한을 강제로 취소할 수 없다.

(4) 대기 상태의 사이클(circular wait)

- 두 개 이상의 프로세스가 자원 접근을 기다리는데, 그 관계에 사이클이 존재

교착상태를 방지하기 위해 위 조건들 가운데 하나를 제거하면 된다.

- 하지만 상당수는 만족되기 어려운 것이라서 까다롭다.
- 대부분의 교착상태 방지 알고리즘은 4번 조건, 즉 대기 상태의 사이클이 발생하는 일을 막는 데 초점이 맞춰져 있다.


# 면접문제

<details>
<summary>프로세스와 스레드의 차이</summary>

**프로세스**

- 실행되고 있는 `프로그램의 인스턴스`
- `CPU 시간`이나 `메모리` 등의 시스템 자원이 할당되는 독립적인 개체
- 각 프로세스는 별도의 주소 공간에서 실행되며, 한 프로세스는 다른 프로세스의 변수나 자료구조에 접근 불가
    - 다른 프로세스의 자원에 접근하려면 프로세스 간 통신(inter-process communication)을 사용
    - 프로세스 간 통신 방법: 파이프, 파일, 소켓 등을 이용한 방법

**스레드**

- 프로세스 안에 존재하며 프로세스의 자원(힙 공간 등)을 공유한다.
- 같은 프로세스 안에 있는 여러 스레드들은 같은 힙 공간을 공유한다.
    - 반면 프로세스는 다른 프로세스의 메모리에 직접 접근할 수 없다.
- 각각의 스레드는 별도의 레지스터와 스택을 갖고 있지만, 힙 메모리는 서로 읽고 쓸 수 없다.

스레드는 프로세스의 특정한 수행 경로와 같다.

- 한 스레드가 프로세스 자원을 변경하면, 다른 이웃 스레드도 그 변경 결과를 즉시 볼 수 있다.
</details>

.

<details>
<summary>문맥 전환(context switch)에 소요되는 시간을 측정하려면 어떻게 해야 할까?</summary>

`문맥 전환` : 두 프로세스를 전환하는 데 드는 시간

- 즉, 대기 중인 프로세스를 실행 상태로 전환하고, 실행 중인 프로세스를 대기 상태나 종료 상태로 전환하는 데 드는 시간
- 멀티태스킹 시 이런 일이 발생한다.
- 운영체제는 대기중인 프로세스의 상태 정보를 메모리에 올리고, 현재 실행 중인 프로세스의 상태 정보는 저장해야 한다.
</details>

.

<details>
<summary></summary>

</details>

.

<details>
<summary></summary>

</details>

.

<details>
<summary></summary>

</details>

.

<details>
<summary></summary>

</details>

.