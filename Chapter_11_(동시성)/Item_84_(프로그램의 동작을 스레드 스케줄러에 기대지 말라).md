# Item 84. 프로그램의 동작을 스레드 스케줄러에 기대지 말라

### 내용 요약 <br>
1. 스레드 스케줄러에 의존하지 말아야 하는 이유
    - `스레드 스케줄러`는 운영체제와 JVM에 의해 제어되며, 특정 스케줄링 정책에 프로그램이 의존하게 되면 `플랫폼에 따라 동작이 달라질 수 있다.` <br><br>

    - `다중 스레드 프로그램`은 스레드 스케줄링 방식에 관계없이 올바르게 동작해야 한다. <br>
      프로그램이 동작을 보장하기 위해서는 스레드의 순서를 예상하거나 의도적으로 지연을 사용하는 방식이 아닌, `명시적인 동기화`나 `동시성 제어 기법`을 사용해야 한다. <br><br>


2. 올바른 동시성 코딩 기법
    - 동기화 기법 사용
        - `synchronized`, `Lock`과 같은 동기화 메커니즘을 사용해 명확하게 스레드 간의 상호작용을 제어해야 한다. <br><br>

    - 대기와 통지를 통한 제어
        - `wait`, `notify`, `notifyAll`과 같은 메소드를 통해 `스레드 간의 명시적인 협력`을 구현해야 한다. <br><br>

    - 동시성 유틸리티 사용 (`java.util.concurrent` 패키지의 동시성 유틸리티)
      - (`ExecutorService`, `CountDownLatch`, `CyclicBarrier 등)를 활용해 스레드 간의 `협력을 명확하게` 설정해야 한다. <br><br>

3. 스레드 우선순위에 대한 오해
    - 스레드 우선순위는 플랫폼마다 다르게 동작하며, 높은 우선순위를 준다고 해서 반드시 더 많은 CPU 시간을 받는 것이 아니다. <br><br>

    - `스레드 우선순위에 의존한 프로그램 설계는 위험`하므로, 우선순위보다는 `명확한 동기화와 실행 순서를 보장하는 메커니즘`을 사용하는 것이 중요하다. <br><br>


4. 스레드 스케줄러에 의존하는 잘못된 사례
    - 특정 스레드가 먼저 실행되기를 기대하거나, 일정 시간 동안 스레드를 `수동적으로 지연`시키는 코드는 예측할 수 없는 결과를 초래할 수 있다. <br>
      이는 테스트 중이나 특정 환경에서는 잘 동작하더라도, `다른 환경에서는 비정상적인 동작을 일으킬 가능성`이 높다. <br><br>

      
5. 신뢰성 높은 다중 스레드 코드 작성법
    - `스레드 간의 협력은 명확한 메커니즘`을 사용해 제어해야 한다. <br>
      스레드 스케줄링을 예상해서 작성하는 코드는 올바른 방식이 아니다. <br><br>

    - 가능한 한 `동시성 프레임워크를 사용`해 스레드 관리를 추상화하는 것이 좋다. <br><br>

<br><br>



### 스레드 스케줄러에 의존하는 잘못된 코드
```java
public class ThreadSchedulerExample {

    private static boolean ready;
    private static int number;
    
    private static class ReaderThread extends Thread {
        public void run() {
            while (!ready) {
                Thread.yield();             // 잘못된 방식 : 스레드 스케줄러에 의존
            }
            System.out.println(number);
        }
    }
    
    public static void main(String[] args) {
        new ReaderThread().start();
        number = 42;
        ready = true;
    }
}
```
위 코드에선 `ready`변수가 `true`가 될 때까지 `Thread.yield()`를 호출하여 CPU 제어권을 다른 스레드에게 넘긴다. <br>
이 방식은 `스레드 스케줄러에 의존`하고 있기 때문에, 프로그램이 의도한 대로 항상 동작한다고 보장할 수 없다. <br>
운영체제나 JVM의 스케줄링 정책에 따라 `ReaderThread`가 영원히 `ready`상태를 기다리며 멈출 수 있다. <br><br>


<br><br>




### 스레드 스케줄러에 의존하는 잘못된 코드
```java
public class ThreadSchedulerExampleFixed {

    private static boolean ready;
    private static int number;
    
    private static synchronized void setReady(boolean value) {
        ready = value;
    }
    
    private static synchronized boolean isReady() {
        return ready;
    }
    
    private static class ReaderThread extends Thread {
        public void run() {
            while(!isReady()) {
                // do nothing
            }
            System.out.println(number);
        }
    }
    
    public static void main(String[] args) {
        new ReaderThread().start();
        number = 42;
        setReady(true);             // 명확한 동기화 사용
    }
}
```
위 코드에선 `ready`변수에 접근할 때 `동기화된 메소드`를 사용하여 `스레드 안전성`을 보장한다. <br>
이렇게하면 `ready`변수에 대한 접근이 스레드 간에 일관되게 이루어지며, 스케줄러에 의존하지 않고 `프로그램 동작이 예측 가능`해진다. 

<br><br>





### 동시성 유틸리티를 사용한 개선된 예시
```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {

   private static final CountDownLatchExample latch = new CountDownLatch(1);
   private static int number;

   private static class ReaderThread extends Thread {
       public void run() {
           try {
               latch.await();               // 스레드 간 협력을 위해 CountDownLatch 사용
           }
           catch (InterruptedException e) {
               Thread.currentThread().interrupt();
           }
           System.out.println(number);
       }
   }
   
   public static void main(String[] args) {
       new ReaderThread().start();
       number = 42;
       latch.countDown();                   // ReaderThread가 실행되도록 신호 보냄
   }
}
```
위 코드에선 `CountDownLatch`를 사용하여 `스레드 간의 협력`을 명확하게 표현한다. <br>
`ReaderThread`는 `latch.await()`를 호출하여 다른 스레드가 `latch.countDown()`을 호출할 때까지 대기하며 <br>
이를 통해, `정확한 실행 순서`를 보장할 수 있다. <br>
이 방식은 스레드 스케줄러에 의존하지 않고 `동시성 제어를 명확하게`처리한다. 

<br><br>



### 정리하자면
1. `스레드 스케줄러`는 운영체제에 따라 다르게 동작하므로, 프로그램이 스레드의 실행 순서에 의존하도록 작성하는 것은 위험하다. <br><br>

2. 대신 `명확한 동기화` 기법과 `동시성 유틸리티`를 사용해 스레드 간의 상호작용을 제어해야 한다. <br><br>

3. 스레드 우선순위나 지연 메커니즘에 의존하지 않고 동시성 프레임워크를 사용해 `신뢰성 있는 다중 스레드 코드를 작성`하는 것이 중요하다.

