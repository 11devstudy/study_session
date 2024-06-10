# 연결리스트

## ✅ 연결리스트 만들기

> 연결리스트는 차례로 연결된 노드를 표현해주는 자료구조이다.
> 

양방향 연결리스트에서 각 노드는 다음 노드와 이전 노드를 함께 가리킨다.

- 배열과 달리 연결리스트에서는 특정 인덱스를 상수 시간에 접근할 수 없다.
- 리스트에서 K번째 원소를 찾고 싶다면 처음부터 K번 루프를 돌아야 한다.

연결리스트의 장점

- 리스트의 시작 지점에서 아이템을 추가하거나 삭제하는 연산을 상수 시간에 할 수 있다는 점

```java
class Node {
	Node next = null;
	int data;
	public Node(int d) {
		data = d;
	}
	
	void appendToTail(int d) {
		Node end = new Node(d);
		Node n = this;
		while (n.next != null) {
			n = n.next;
		}
		n.next = end;
	}
}
```

## ✅ 단방향 연결리스트에서 노드 삭제

> 연결리스트에서 노드를 삭제하는 연산은 꽤 직관적이다.
> 

단방향 연결리스트

- 노드 n이 주어지면, 그 이전 노드 prev를 찾아 prev.next를 n.next와 같도록 설정한다.

양방향 연결 리스트

- n.next가 가리키는 노드를 갱신하여 n.next.prev가 n.prev와 같도록 설정해야 한다.
- 필요할 경우 head와 tail 포인트도 갱신해야 한다.

```java
Node deleteNode(Node head, int d) {
	Node n = head;
	if (n.data == d) {
		return head.next; // move head
	}
	
	while (n.next != null) {
		if (n.next.data == d) {
			n.next = n.next.next;
			return head; // not move head
		}
		n = n.next;
	}
	return head;
}
```

## ✅  Runner 기법

> Runner(부가 포인터)는 연결리스트 문제에서 많이 활동되는 기법이다.
> 

연결리스트를 순회할 때 두 개의 포인터를 동시에 사용하는 것이다.

## ✅  재귀 문제

> 연결리스트 관련 문제 가운데 상당수는 재귀 호출에 의존한다.
> 

재귀 호출 깊이가 n이 될 경우, 해당 재귀 알고리즘이 적어도 $O(n)$ 만큼의 공간을 사용한다는 사실을 기억하자.

- 모든 재귀 알고리즘은 반복적 형태로도 구현될 수 있긴 하지만, 반복적 형태로 구현하면 한층 복잡해질 수 있다.