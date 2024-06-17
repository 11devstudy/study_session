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

<details>
<summary></summary>

</details>

.

<details>
<summary></summary>

</details>

.