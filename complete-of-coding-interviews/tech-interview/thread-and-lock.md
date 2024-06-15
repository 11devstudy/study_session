# Thread and Lock

> 자바의 모든 스레드는 `java.lang.Thread` 클래스 객체에 의해 생성되고 제어된다.
> 

독립적인 응용 프로그램이 실행될 때, main() 메서드를 실행하기 위한 하나의 user thread 가 자동으로 만들어지는데, 이 스레드를 main thread 라고 부른다.

자바에서 스레드를 구현하는 방법으로는 두 가지가 있다.

- `java.lang.Runnable` 인터페이스 구현하기
- `java.lang.Thread` 클래스를 상속받기

## java.lang.Runnable 인터페이스 구현하기

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