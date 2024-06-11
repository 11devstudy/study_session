# 스택과 큐

### ✅ 스택 구현하기

> 스택 자료구조는 말 그래도 데이터를 쌓아 올린다는 의미
> 
- LIFO(Last-In-First-Out)에 따라 자료를 배열
- 가장 최근에 스택에 추가한 항목이 가장 먼저 제거

스택의 연산

- pop(): 스택에서 가장 위에 있는 항목을 제거
- push(item): item 하나를 스택의 가장 윗 부분에 추가
- peek(): 스택의 가장 위에 있는 항목을 반환
- isEmpty(): 스택이 비어 있을 때 true 반환

배열과 달리 스택은 상수 시간에 1번째 항목에 접근 불가

- 데이터 추가/삭제 연산은 상수 시간에 가능
- 스택이 유용한 경우는 재귀 알고리즘을 사용할 때

```java
public class MyStact {
	private static class StackNode {
		private T data;
		private stackMode next;
		public stackNode(T data) {
			this.data = data;
		}
	}
	
	private StackNode top;
	public T pop() {
		if (top == null) throw new EmptyStackException();
		T item = top.data;
		top = top.next;
		return item;
	}
	
	public void push(T item) {
		StackNode t = new StackNode(item);
		t.next = top;
		top = t;
	}
	
	public T peek() {
		if (top == null) throw new EmptyStackException();
		return top.data;
	}
	
	public boolean isEmpty() {
		return top == null;
	}
}
```

### ✅ 큐 구현하기

> FIFO(First-In-First-Out)
큐에 저장되는 항목들은 큐에 추가되는 순서대로 제거
> 

큐의 연산

- add(item): item을 리스트의 끝부분에 추가
- remove(): 리스트의 첫 번째 항목을 제거
- peek(): 큐에서 가장 위에 있는 항목을 반환
- isEmpty(): 큐가 비어 있을 때에 true 반환

큐는 너비 우선 탐색을 하거나 캐시를 구현하는 경우 종종 사용

- 처리해야 할 노드의 리스트를 저장하는 용도로 큐를 사용
- 노드를 하나 처리할 때마다 해당 노드와 인접한 노드들을 큐에 다시 저장

```java
public class MyQueue {
	private static class QueueNode {
		private T data;
		private QueueNode next;
		public QueueNode(T data) {
			this.data = data;
		}
	}
	
	private QueueNode first;
	private QueueNode last;
	
	public void add(T item) {
		QueueNode t = new QueueNode(item);
		if (last != null) {
			last.next = t;
		}
		last = t;
		if (first == null) {
			first = last;
		}
	}
	
	public T remove() {
		if (first == null) throw new NoSuchElementException();
		T data = first.data;
		first = first.next;
		if (first == null) {
			last = null;
		}
		return data;
	}
	
	public T peek() {
		if (first == null) throw new NoSuchElementException();
		return first.data;
	}
	
	public boolean isEmpty() {
		return first == null;
	}
}
```