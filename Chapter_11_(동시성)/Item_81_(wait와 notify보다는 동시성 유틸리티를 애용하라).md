# Item 81. wait와 notify보다는 동시성 유틸리티를 애용하라

### 내용 요약 <br>
1. `wait`와 `notify`의 한계
    - `wait`와 `notify`는 동시성 프로그래밍에서 중요한 저수준 도구이지만, 제대로 사용하기 어렵고 `잠재적인 버그`가 발생할 수 있다. <br><br>

    - 이들은 `모니터 락`과 함께 사용되는데 모든 메소드에서 올바른 동기화가 이루어져야 하며 <br>
      그렇지 않으면 `교착 상태`나 `잘못된 동작`이 발생할 수 있다. <br><br>

    - 예외가 발생했을 때, 락을 해제하지 않고 반환되면 `락을 잃어버리는 문제`가 생길 수 있다. <br><br>

2. 고수준 동시성 유틸리티 사용의 장점
    - `java.util.concurrent` 패키지에서 제공하는 `고수준 동시성 유틸리티`는 <br>
      `wait`와 `notify`보다 `간결하고 안전하게` 동기화를 관리할 수 있게 해준다. <br><br>

    - 이러한 유틸리티는 `wait`와 `notify`에서 발생할 수 있는 `경쟁 상태`, `교착 상태`, `기아 상태` 등의 문제를 해결해준다. <br><br>


3. 주요 동시성 유틸리티
    - `CountDownLatch` : 특정 작업이 완료될 때 까지 다른 스레드가 기다리도록 하는 유틸리티.
    - `Semaphore` : 리소스에 대한 접근을 제한할 때 유용
    - `CyclicBarrier` : 다수의 스레드가 동시에 특정 지점에 도달할 때까지 기다리도록 함
    - `Exchanger` : 두 스레드가 데이터를 교환하느 데 사용
    - `BlockingQueue` : 다중 스레드 간의 데이터를 안전하게 교환할 수 있게 함
    - `Concurrent Collections` : `ConcurrentHashMap` 등은 스레드 간 안전한 컬렉션 접근을 제공

<br>

4. `ReentrantLock` 과 `Condition`
    - `ReentrantLock`과 `Condition`은 `synchronized`와 `wait/notify`의 대안으로 더 세밀한 락 관리가 가능하다. <br><br>

    - `Condition` 객체는 `wait/notify` 대신 사용할 수 있는 유연한 동기화 기법을 제공한다. <br>
      각 락에 대해 여러 조건을 관리할 수 있다. <br><br>

<br><br>



### `wait`와 `notify`를 사용한 코드
```java
class WaitNotifyExample {

    private final Object lock = new Object();
    private boolean conditionMet = false;
    
    public void waitForCondition() throws InterruptedException {
        synchronized (lock) {
            while(!conditionMet) {
                lock.wait();
            }
        }
    }
    
    public void signalCondition() {
        synchronized (lock) {
            conditionMet = true;
            lock.notify();
        }
    }
}
```
위 코드에선 `wait`와 `notify`를 사용하여 조건이 충족될 때까지 기다렸다가 조건이 충족되면 신호를 보낸다. <br>
하지만 이러한 코드는 `동기화에 대한 세밀한 관리`가 필요하고 잘못된 사용으로 인해 `경쟁 조건`이나 `교착 상태`가 발생할 수 있다. <br><br>

<br><br>



### `CountDownLatch`를 사용한 코드
```java
import java.util.concurrent.CountDownLatch;

class CountDownLatchExample {

    private final CountDownLatch latch = new CountDownLatch(1);
    
    public void waitForCondition() throws InterruptedException {
       latch.await();                   // 조건이 충족될 때까지 대기
    }
    
    public void signalCondition() {
        latch.countDown();              // 조건이 충족됨을 신호
    }
}
```
위 코드에선 `CountDownLatch`는 `스레드가 안전하게 특정 조건을 기다리게 하거나, 조건이 충족되었음을 신호`할 때 유용하다. <br>
`wait`/`notify`보다 간결하고 `더 안전하게 동작`한다. <br>
`latch.await()`는 다른 스레드에서 `countDown()`이 호출될 때까지 대기한다. <br><br>

<br><br>


### `CyclicBarrier`를 사용한 코드
```java
import java.util.concurrent.CyclicBarrier;

class CyclicBarrierExample {

    private final CyclicBarrier barrier = new CyclicBarrier(3, () -> {
        System.out.println("All tasks finished!");
    });
    
    public void performTask() throws InterruptedException, java.util.concurrent.BrokenBarrierException {
       System.out.println("Task executed by : " + Thread.currentThread().getName());
       barrier.await();                 // 3개의 스레드가 모두 도착할 때까지 대기
    }
    
    public static void main(String[] args) {
        CyclicBarrierExample example = new CyclicBarrierExample();
        Runnable task = () -> {
            try {
                example.performTask();
            } catch(Exception e) {
                e.printStackTrace();
            }
        };
        
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
    }
}
```
위 코드에선 `CyclicBarrier`는 `여러 스레드가 특정 지점에서 동시에 대기`하다가 정해진 수의 스레드가 모두 도달하면 한꺼번에 진행하게 한다. <br>
위 코드는 `세 개의 스레드가 모두 작업을 마치면` "All tasks finished!"를 출력하도록 작성했다.

<br><br>



### `BlockingQueue`를 사용한 생산자-소비자 예제
```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

class ProducerConsumerExample {

    private final BlockingQueue<Integer> queue = new LinkedBlockingQueue<>();
    
    public void produce(int value) throws InterruptedException {
        queue.put(value);                   // 큐가 꽉 차면 대기
        System.out.println("Produced : " + value);
    }
    
    public int consume() throws InterruptedException {
        int value = queue.take();           // 큐가 비어있으면 대기
        System.out.println("Consumed : " + value);
        return value;
    }
    
    public static void main(String[] args) {
        ProducerConsumerExample example = new ProducerConsumerExample();
        
        Runnable producer = () -> {
            try {
                for(int i=0; i<10; i++) {
                    example.produce(i);
                }
            }
            catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };
        
        Runnable consumer = () -> {
            try {
                for(int i=0; i<10; i++) {
                    example.consume();
                }
            }
            catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };
        
        new Thread(producer).start();
        new Thread(consumer).start();
    }
}
```
위 코드에선 `BlockingQueue`는 `스레드 간 안전하게 데이터를 주고 받을 수 있도록` 해준다. <br>
생산자-소비자 패턴에서 `큐가 가득 찼을 때는 생산자가 대기`하고 `큐가 비었을 때는 소비자가 대기`하는 방시긍로 자연스럽게 동기화가 이루어진다. <br><br>

<br><br>


### 정리하자면
1. `wait`과 `notify`는 강력하지만 이를 사용하여 동기화를 직접 관리하는 것은 `복잡하고 오류를 일으키기 쉽다`.
2. `java.util.concurrent` 패키지의 고수준 동시성 유틸리티를 사용하면 더 쉽고 안전하게 스레드 동기화 작업을 처리할 수 있다.
3. `CountDownLatch`, `CyclicBarrier`, `BlockingQueue` 등은 일반적인 동기화 작업을 간결하고 효율적으로 처리할 수 있게 해준다.


