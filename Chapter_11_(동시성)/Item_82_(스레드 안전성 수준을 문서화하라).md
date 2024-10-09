# Item 82. 스레드 안전성 수준을 문서화하라

### 내용 요약 <br>
1. 스레드 안전성의 중요성
    - 여러 스레드가 동시에 같은 객체에 접근하는 경우, `스레드 안전성`을 확보하는 것은 매우 중요하다. <br><br>

    - 스레드 안전성을 보장하는 클래스와 메소드는 `어떤 동작들이 안전하게 이루어지는지, 어떤 동작들이 안전하지 않은지 명확히 문서화` 되어야 한다. <br><br>


2. 스레드 안전성의 다양한 수준
    - 불변(immutable)
      - 객체가 변경되지 않으며, 여러 스레드에서 동시에 안전하게 사용될 수 있다. <br>
        불변 객체는 스레드 안전성이 필요하지 않다. <br><br>
    
    - 무조건적 스레드 안전(unconditionally thread-safe)
      - 동기화 없이도 여러 스레드에서 안전하게 사용할 수 있는 객체이다. <br>
        `ConcurrentHashMap` 같은 클래스가 이에 해당한다. <br><br>

    - 조건부 스레드 안전 (conditionally thread-safe)
      - 일부 메소드는 동기화 없이 안전하게 사용할 수 있지만, 다른 메소드들은 외부 동기화가 필요하다. <br><br>
    
    - 스레드 안전하지 않음 (not thread-safe)
      - 클래스가 기본적으로 스레드에 안전하지 않으며, 여러 스레드에서 사용하려면 외부 동기화가 필요하다. <br><br>
    
    - 스레드 절대적 (thread-hostile)
      - 스레드 간 경쟁 상태를 일으켜 여러 스레드에서 사용하면 심각한 문제를 일으킬 수 있는 클래스이다. <br><br>


3. 문서화의 중요성
    - 스레드 안전성 수준을 명확히 문서화하지 않으면, 개발자들이 잘못된 가정을 하게 되어 `치명적인 동기화 문제`를 유발할 수 있다. <br><br>
   
      - 클래스와 메소드가 제공하는 스레드 안전성의 `정확한 범위`와 `외부 동기화 필요성`을 `@ThreadSafe`, `@GuardedBy` 등의 주석이나 <br>
        JavaDoc을 통해 설명해야 한다. <br><br>


4. 상태 변화 및 동기화 방법 설명
    - 객체의 상태가 스레드 간 안전하게 공유될 수 있도록 변경하는 방법이나 `상태 변화를 설명`하는 것도 문서화에 포함되어야 한다. <br><br>

    - 특히 `volatile`이나 `동기화 블록`을 사용할 때, 어떤 상태가 어떻게 보호되는지 명확히 기록해야 한다. <br><br>

<br><br>


### 불변 객체의 스레드 안전성
```java
public final class ImmutableClass {

    private final int value;
    
    public ImmutableClass(int value) {
        this.value = value;
    }
    
    public int getValue() {
        return value;
    }
}
```
위 코드에선 `ImmutableClass`는 `불변 객체`로 내부 상태가 변경되지 않기 때문에 `여러 스레드에서 안전하게 사용할 수 있다.` <br>
불변 객체는 추가적인 동기화 없이도 안전성을 보장할 수 있다. <br><br>

<br><br>


### 조건부 스레드 안전성
```java
import java.util.Vector;

public class ConditionallyThreadSafeClass {

    private final Vector<Integer> vector = new Vector<>();
    
    // 동기화가 필요하지 않은 메소드
    public synchronized void add(Integer value) {
        vector.add(value);
    }
    
    // 동기화가 필요할 수도 있는 메소드
    public Integer get(int index) {
        return vector.get(index);
    }
}
```
위 코드에선 `add`메소드는 `내부적으로 동기화`되어 있기 때문에 여러 스레드에서 안전하게 호출할 수 있다. <br>
하지만 `get`메소드는 `외부 동기화`가 필요할 수 있다. 이런 경우, `조건부 스레드 안전성`을 제공하며, 이를 문서화해야 한다. <br><br>

<br><br>


### 스레드 안전하지 않은 클래스
```java
public class NotThreadSafeClass {

    private int value;
    
    public void increment() {
        value++;
    }
    
    public int getValue() {
        return value;
    }
}
```
위 코드에선 `NotThreadSafeClass`는 스레드 안전하지 않다. <br>
여러 스레드가 동시에 `increment`를 호출하면 `경쟁 상태`가 발생할 수 있다. <br>
이런 클래스는 스레드 간 동기화를 보장하지 않기 때문에 `외부 동기화가 필요`하다는 사실을 문서화해야 한다. <br><br>

<br><br>



### 문서화 예시
```java
/**
 * 이 클래스는 thread-safe 하다. 
 * 외부 동기화 없이 여러 스레드가 add 메소드를 호출하여 사용할 수 있다. 
 * */

public class ThreadSafeList {

    private final List<Integer> list = Collections.synchronizedList(new ArrayList<>());
    
    public void add(Integer value) {
        list.add(value);
    }
    
    public Integer get(int index) {
        return list.get(index);
    }
}
```
위 코드에선 클래스가 `동기화된 리스트`를 사용하고 있으므로 여러 스레드에서 안전하게 접근할 수 있다.

<br><br>


### 정리하자면
1. `스레드 안전성 수준을 명확하게 문서화`하는 것은 매우 중요하며 <br>
   클래스나 메소드가 어떻게 동작하고 어떤 동기화가 필요한 지 개발자들이 쉽게 이해할 수 있도록 도와준다. <br><br>

2. Java의 `고수준 동시성 유틸리티`를 적극 활용하여 스레드 안전성을 보장하고, 그 `특성을 문서화`함으로써 코드의 가독성과 유지보수성을 높일 수 있다. <br><br>

3. 문서화를 통해 `불필요한 오해`를 줄이고, 사용자에게 `명확한 사용 지침`을 제공하는 것이 중요하다. <br><br>




