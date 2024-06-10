# Java

<details>
<summary>오버로딩 vs 오버라이딩</summary>

오버로딩
    
두 메서드가 같은 이름을 갖고 있으나 인자의 수나 자료구조형이 다른 경우

오버라이딩

상위 클래스의 메서드와 이름과 용례가 같은 함수를 하위 클래스에 재정의하는 것
</details>

.

<details>
<summary>컬렉션 프레임워크</summary>

ArrayList

- 동적으로 크기가 조절되는 배열
- 새 원소를 삽입하면 크기가 늘어난다

Vector

- ArrayList와 비슷하지만 동기화되어 있다는 차이

LinkedList

- 순환자 사용 방법

```java
Itorator iter = list.iterator();
while (iter.hasNext()) {
	.. iter.next();
}
```

HashMap

- 광범위하게 사용되는 컬렉션

```java
HashMap map = new HashMap();
map.put("one","uno");
map.put("two","dos");
map.get("one");
```
</details>

.

<details>
<summary>private 생성자</summary>

상속 관점에서 생성자를 private로 선언하면?
    
생성자가 private로 선언된 A class는 A의 private 메서드에 접근이 가능해야만 생성자를 호출할 수 있다는 것을 의미한다. 

A의 private 메서드와 생성자는 A의 내부 클래스만 접근 가능하다. 
</details>

.

<details>
<summary>finally에서의 반환</summary>

finally 블록은 try 블록이 종료되는 순간 실행된다.

finally 블록이 실행되지 않는 경우

- try/catch 블록에서 가상머신이 종료되는 경우
- try/catch 를 수행하던 스레드가 죽는 경우
</details>

.

<details>
<summary>final, finally, finalize 차이</summary>

### ℹ️ final

변수나 메서드 또는 클래스가 변경 불가능하도록 만든다. 

- 사용 문맥에 따라 의미가 달라진다.
- 원시 변수: 변경 불가
- 참조 변수: 힙 내의 다른 객체를 가리키도록 변경 불가
- 메서드: 오버라이드 불가
- 클래스: 하위 클래스 적용 불가

### ℹ️  finally

try/catch 블록이 종료될 때 항상 실행되는 코드 블록

- 종종 뒷마무리를 위한 코드를 작성

### ℹ️  finalize

GC가 더 이상의 참조가 존재하지 않는 객체를 메모리에서 삭제하겠다고 결정하는 순간 호출

- GC가 객체 해제 전 호출하는 메서드
</details>

.

<details>
<summary>TreeMap, HashMap, LinkedHashMap 차이</summary>

> 세 가지 모두 key 에서 value 로의 대응 관계가 있고, 키를 기준으로 순회 가능
> 

### ℹ️ TreeMap

> 검색과 삽입에 $O(logN)$ 시간이 소요
> 
- 키는 정렬되어 있으므로 정렬된 순서로 키를 순회
- 키는 반드시 Comparable 인터페이스를 구현하고 있어야 한다.
- 레드-블랙 트리로 구현

### ℹ️ HashMap

> 검색과 삽입에 $O(1)$ 시간 소요
> 
- 키를 기준으로 순회할 때 키의 순서는 무작위
- 구현은 연결리스트로 이루어진 배열로 구성

### ℹ️ LinkedHashMap

> 검색과 삽입에 $O(1)$ 시간 소요
> 
- 키는 삽입한 순서대로 정렬
- 양방향 연결 버킷(double-linked bucket)으로 구현

{1, -1, 0} 순서로 Map 에 데이터를 넣는다고 가정해 보자.

| HashMap       | LinkedHashMap | TreeMap    |
| ------------- | ------------- | ---------- |
| (임의의 순서) | {1, -1, 0}    | {-1, 0, 1} |

> 각기 어느 때에 사용하는게 좋을까?
> 

TreeMap

- 이름과 Person 객체 사이에 대응 관계를 만들고 주기적으로 이름순으로 사람을 출력하고 싶을 경우
- 이름이 주어졌을 때 그 다음 10명의 사람을 출력(More)하고 싶을 경우

**LinkedHashMap**

- `삽입한 순서대로 키를 정렬`하고 싶을 경우 유용
- ex. 캐시를 구현할 때 가장 오래된 아이템을 먼저 삭제하고 싶은 경우 유용

**HashMap**

- 일반적으로 별다른 이유가 없을 경우 사용
- 일반적으로 빠르고 오버헤드가 적다.
</details>
    
.





<details>
<summary></summary>

</details>