# Item 78. 공유 중인 가변 데이터는 동기화해 사용하라

### 내용 요약 <br>
1. 동기화의 중요성
    - 멀티스레드 환경에서 `여러 스레드가 동시에 공유 자원에 접근`할 때, 동기화하지 않으면 `상태 불일치`가 발생할 수 있다. <br>
      동기화는 여러 스레드가 가변 데이터에 안전하게 접근할 수 있도록 보장하는 수단이다. <br><br>

    - 자바에서 동기화는 `synchronized` 키워드 또는 `락(lock)`을 이용해 구현할 수 있다. <br><br>

2. 동기화의 역할
    - 동기화는 `스레드 간의 가시성 문제`를 해결한다. 즉, 한 스레드에서 발생한 변경이 다른 스레드에서 즉시 반영되도록 보장한다. <br><br>

    - 또한, `경쟁조건(race condition)`을 방지한다. <br>
      경쟁 조건이 발생하면 여러 스레드가 동시에 공유자원을 수정하는 과정에서 예기치 못한 결과가 발생할 수 있다. <br><br>


3. 동기화가 필요한 상황
    - 동기화는 성능에 약간의 비용을 수반하지만, 이를 통해 시스템의 `정확성`과 `안정성`을 보장할 수 있다. <br><br>

    - 불필요한 동기화는 피해야 하지만, 가변 데이터를 안전하게 보호하기 위한 필수적인 경우라면 동기화는 반드시 해야 한다. <br><br>


<br><br>


### 동기화 없이 공유 데이터를 사용하는 코드(문제가 있는 코드)
```java
public class Counter {

    private int count = 0;
    
    public void increment() {
        count++;                    // 스레드 안전하지 않음
    }
    
    public int getCount() {
        return count;
    }
}
```
위 코드에선 `increment` 메소드는 `count` 변수를 증가시키는 단순한 작업을 수행하지만 `스레드 세이프`하지 않다. <br><br>

여러 스레드가 동시에 `increment()`를 호출하면, `경쟁 조건`이 발생하여 `count` 값이 일관되지 않게 된다. <br><br>

<br><br>



### 동기화를 추가한 안전한 코드
```java
public class Counter {

    private int count = 0;
    
    public synchronized void increment() {
        count++;                            // 스레드 안전
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```
위 코드에선 `syncronized` 키워드를 사용해 메소드에 동기화를 적용했다. <br>
이제 `increment()`와 `getCount()` 메소드를 호출하는 `여러 스레드가 동기화 블록에서 실행`되어 동시에 접근할 수 없게 된다. <br>
이를 통해 `경쟁 조건`이 방지되고, 공유 데이터가 안전하게 보호된다. <br><br>

<br><br>


### `Lock`을 사용한 동기화
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Counter {

    private final Lock lock = new ReentrantLock();
    private int count = 0;
    
    public void increment() {
        lock.lock();
        try {
            count++;                        // 스레드 안전
        }
        finally {
            lock.unlock();
        }
    }
    
    public int getCount() {
        lock.lock();
        try {
            return count;
        }
        finally {
           lock.unlock();
        }
    }
}
```
위 코드에선 `ReentrantLock`을 사용하여 동기화를 구현한 예시이다. <br>
`syncronized`와 유사하게 작동하지만 `Lock` 인터페이스는 더 정교한 도오기화 메커니즘을 제공한다. <br><br>

이 방식은 복잡한 시나리오에서 `동기화에 ㅐㄷ한 세부 제어`가 필요한 경우 사용할 수 있다. <br><br>

<br><br>


### 정리하자면
1. `동기화는 멀티스레드 환경에서 가변 데이터를 안전하게 다루기 위한 필수적인 기술`이다. <br>
   공유자원에 접근하는 여러 스레드를 동기화하지 않으면 경쟁 조건이나 데이터 손상이 발생할 수 있다. <br><br>

2. `syncronized`키워드나 `Lock`을 사용하여 가변 데이터에 대한 동기화를 적절히 구현함으로써, 멀티스레드 환경에서도 `일관성 있고 안전한 프로그램`을 작성할 수 있다. <br><br>

3. 동기화는 성능에 약간의 영향을 줄 수 있지만, `안정성과 정확성을 보장하지 위해서는 필수적`인 요소이다. <br><br> 

