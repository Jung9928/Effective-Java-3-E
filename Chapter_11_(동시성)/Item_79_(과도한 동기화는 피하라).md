# Item 79. 과한 동기화는 피하라

### 내용 요약 <br>
1. 교착 상태(Deadlock)
   - 교착 상태(Deadlock)
       - 여러 스레드가 서로의 락을 기다리는 상황이 발생할 수 있다. <br>
         교착 상태는 시스템 전체가 멈추는 결과를 초래하므로 매우 치명적이다. <br><br>
     
   - 활성화 문제(Livelock) 
     - 교착 상태와 달리, 여러 스레드가 락을 확보하지 못해 끊임없이 활동하지만, 실제로 아무런 진행을 하지 못하는 상태입니다.

   - 성능 저하
       - 불필요하게 많은 동기화는 성능을 저하시킨다. 
         특히, 동기화된 메소드에 스레드가 동시에 접근하려고 할 때, 병목이 발생할 수 있다. <br><br>

   - 예기치 않은 동작
      - 잘못된 동기화로 인해 `무한 대기` 상태나 프로그램이 예측 불가능한 방식으로 동작할 수 있다. <br><br>

2. 잘못된 동기화로 인한 문제 예방
   - 가능한 한 `동기화의 범위를 최소화`해야 한다. <br>
     동기화 블록은 꼭 필요한 부분에만 적용하고, 너무 넓게 적용하는 것을 피해야 한다. <br><br>

   - `객체 내부 상태의 노출을 방지`해야 한다. <br>
     동기화된 객체가 외부에 노출되면, 동기화를 제대로 하지 않은 부분에서 문제가 발생할 수 있다. <br><br>

   - `공유되지 않는 자원에 동기화를 적용할 필요가 없음`을 인식해야 한다. <br>
     동기화는 오직 `여러 스레드가 접근할 수 있는 공유 자원`에만 적용해야 한다. <br><br>


3. 공유 자원에만 동기화 적용
   - 동기화는 여러 스레드가 접근하는 `공유 가변 자원`에만 필요하다. <br>
     `불변 객체`나 `지역 변수`는 동기화가 필요하지 않다. <br><br>
   
   - 너무 자주 동기화하거나 불필요하게 큰 범위에 동기화를 걸면 `경쟁 상태를 줄이기는 커녕 성능을 저하시킬 뿐`이다.
   
<br><br>



### 과도한 동기화가 적용된 코드 (비효율적)
```java
public class SynchronizedList<E> {

    private final List<E> list = new ArrayList<>();
    
    // 모든 메소드를 동기화
    public synchronized void add(E e) {
        list.add(e);
    }
    
    public synchronized void remove(E e) {
        list.remove(e);
    }
    
    public synchronized E get(int index) {
        return list.get(index);
    }
    
    public synchronized int size() {
        return list.size();
    }
}
```
위 코드에선 내부 `List`객체의 모든 접근 메소드가 `동기화`되어 있다. <br>
그러나 이렇게 모든 메소드에 동기화를 걸면 `성능 저하`와 함께 여러 스레드가 메소드를 사용할 때, `경쟁이 발생`하여 병목현상이 생길 수 있다. <br><br>

<br><br>




### 필요한 범위에서만 동기화한 개선된 코드
```java
public class SynchronizedList<E> {

    private final List<E> list = new ArrayList<>();
    
    // 최소한의 동기화 블록만 사용
    public void add(E e) {
        synchronized (list) {
            list.add(e);
        }
    }
    
    public void remove (E e) {
        synchronized (list) {
            list.remove(e);
        }
    }
    
    public E get(int index) {
        synchronized (list) {
            return list.get(index);
        }
    }
    
    public int size() {
        synchronized (list) {
            return list.size();
        }
    }
}
```
위 코드에선 메소드 전체를 동기화하지 않고 공유 자원인 `list`에 대한 접근을 동기화 블록으로 보호하고 있다. <br>
이는 필요 최소한의 동기화만 적용한 것으로 `불필요한 성능 저하를 방지`하면서도 동기화 문제를 해결할 수 있는 방식이다. <br><br>

<br><br>



### 잘못된 동기화로 인한 교착 상태의 예
```java
public class DeadlockExample {

    private final Object lock1 = new Object();
    private final Object lock2 = new Object();

    public void method1() {
        synchronized (lock1) {
            // 다른 스레드가 lock2를 획득하기 전까지 대기
            synchronized (lock2) {
                // 작업
            }
        }
    }


    public void method2() {
        synchronized (lock2) {
            // 다른 스레드가 lock1을 획득하기 전까지 대기
            synchronized (lock1) {
                // 작업
            }
        }
    }
}
```
위 코드에선 `교착 상태(deadlock)`가 발생할 가능성이 있다. <br>
한 스레드가 `method1`을 호출하고, 다른 스레드가 `method2`를 호출하면 `두 스레드가 서로의 락을 기다리게 되면서` 시스템이 멈추는 상황이 발생할 수 있다.

<br><br>



### 교착 상태 방지를 위해 개선된 코드
```java
public class NoDeadlockExample {

    private final Object lock1 = new Object();
    private final Object lock2 = new Object();

    public void method1() {
        synchronized (lock1) {
            // 다른 스레드가 lock2를 획득하기 전까지 대기
            synchronized (lock2) {
                // 작업
            }
        }
    }


    public void method2() {
        synchronized (lock1) {                  // 동일한 순서로 락을 획득
            // 다른 스레드가 lock2을 획득하기 전까지 대기
            synchronized (lock2) {
                // 작업
            }
        }
    }
}
```
위 코드에선 `모든 스레드가 동일한 순서로 락을 획득`하도록 하여 교착 상태를 방지한다. <br>
항상 동일한 순서로 자원에 접근하면 스레드 간의 충돌을 줄이고 교착 상태를 예방할 수 있다. <br><br>

<br><br>


### 정리하자면
1. 동기화는 멀티스레드 프로그래밍에 필수적이지만 `과도한 동기화는 피해야`한다. <br>
   불필요한 동기화는 성능 저하, 교착상태 그리고 예상치 못한 동작을 유발할 수 있다. <br><br>

2. `동기화 범위를 최소화`하고 `가변 자원에만 적용`하며 `교착 상태를 방지하기 위한 적절한 락 관리`가 중요하다. <br><br>

3. 불필요한 동기화를 줄임으로써 성능을 최적화하고 프로그램의 안정성을 높일 수 있다. <br><br>

