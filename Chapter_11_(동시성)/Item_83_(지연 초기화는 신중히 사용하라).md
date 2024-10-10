# Item 83. 지연 초기화는 신중히 사용하라

### 내용 요약 <br>
1. 지연 초기화의 정의
    - 지연 초기화는 객체나 리소스가 `실제로 필요해질 때까지 초기화를 미루는 기법`이다. <br>
      이를 통해, 프로그램 시작 시 성능을 최적화할 수 있다. <br><br>

    - 그러나 성능 상의 이유만으로 지연 초기화를 사용하는 것은 좋지 않고, `불필요한 복잡성을 초래할 수 있기 때문에 신중히 선택`해야 한다. <br><br>


2. 필드의 초기화 전략
    - 일반적인 초기화(Eager Initialization)
        - 객체가 생성될 때 즉시 모든 필드를 초기화하는 방법이다. <br>
          대부분의 경우, 단순하고 효율적이다. <br><br>

    - 지연 초기화(Lazy Initialization)
        - 필드가 처음 필요해질 때 비로소 초기화하는 방법이다. <br>
          필드의 초기화 비용이 클 때, 해당 필드가 `사용되지 않을 가능성`이 있을 때 사용을 고려할 수 있다. <br><br>

3. 지연 초기화의 사용 시점
    - 비용이 큰 초기화
      - 초기화 비용이 매우 클 경우, 지연 초기화를 고려할 수 있지만, 필드가 `항상 사용되는 경우`에는 일반적인 초기화가 더 적합하다. <br><br>

    - 상호 의존 필드
      - 여러 필드들이 서로 의존할 경우, 지연 초기화는 `교착 상태(deadlock)`을 일으킬 수 있으므로 주의해야 한다. <br><br>


4. 스레드 안전성 문제
    - 지연 초기화는 `스레드 안전성` 문제가 발생할 수 있다. 여러 스레드에서 필드를 동시에 초기화하려고 하면 `경쟁 상태`가 발생할 수 있기 때문에 <br>
      적절한 `동기화`가 필요하다. <br><br>

    - 성능을 최적화하는 동시에 스레드 안전성을 유지하기 위해 `이중 검사 락킹(double-checked locking)`기법을 사용할 수 있다. <br><br>


5. 세 가지 지연 초기화 방식
   - 정적 필드에 대한 초기화
     - `정적 초기화 블록`을 사용해 스레드 안전하게 초기화하거나, `LazyHolder` 패턴을 사용할 수 있다. <br><br>
     
   - 인스턴스 필드에 대한 지연 초기화
     - 동기화된 접근 또는 이중 검사 락킹을 사용한다. <br><br>

   - `volatile` 키워드를 사용해 스레드 안전성을 확보하면서도 불필요한 동기화를 피하는 방법이 있다. <br><br>

<br><br>



### 일반적인 초기화
```java
public class EagerInitialization {

    private final FieldType field = computeFieldValue();
    
    private static FieldType computeFieldValue() {
        
        // 초기화 작업
        return new FieldType();
    }
}
```
위 코드에선 `일반적인 초기화 방식`으로 객체가 생성될 때 필드가 즉시 초기화된다. <br>
대부분의 경우, 이 방식이 간단하고 효율적이다. <br><br>

<br><br>




### 지연 초기화 (Lazy Initialization)
```java
public class LazyInitialization {

    private FieldType field;
    
    private synchronized FieldType getField() {
        if(field == null) {
            field = computeFieldValue();
        }
        return field;
    }
    
    private static FieldType computeFieldValue() {
        // 초기화 작업
        return new FieldType();
    }
}
```
위 코드에선 `field`가 처음 요청될 때만 초기화 된다. (지연 초기화) <br>
메소드에 `synchronized`를 사용하여 `스레드 안전성`을 확보하고 있다. <br>
이 방식은 필드가 자주 사용되지 않거나 초기화 비용이 큰 경우에 적합하다. <br><br>

<br><br>





### 이중 검사 락킹 (Double-Checked Locking)과 `volatile` 사용

```java
public class DoubleCheckedLocking {

   private volatile FieldType field;

   public FieldType getField() {
       if(field == null) {              // 1차 검사 (락 없이 검사)
           synchronized (this) {
               if(field == null) {      // 2차 검사 (락을 검고 다시 검사)
                   field = computeFieldValue(); 
               }
           }
       }
       
       return field;
   }
   
   private static FieldType computeFieldValue() {
       // 초기화 작업
      
       return new FieldType();
   }
}
```
위 코드에선 `이중 검사 락킹` 기법을 사용해, `field`가 초기화되지 않은 경우에만 동기화를 적용한다. <br>
`volatile`키워드를 사용하여 `스레드 간의 가시성 문제`를 해결한다. <br>
이 방식은 `성능 최적화`와 `스레드 안전성`을 동시에 고려할 수 있는 방법이다. <br><br>

<br><br>



### 정적 필드의 지연 초기화 (LazyHolder 패턴)
```java
public class LazyHolder {
    
   private static class Holder {
       static final FieldType INSTANCE = computeFieldValue();
   }
   
   public static FieldType getInstance() {
       return Holder.INSTANCE;
   }
   
   private static FieldType computeFieldValue() {
       
       // 초기화 작업
       return new FieldType();
   }
}
```
위 코드에선 `LazyHolder` 패턴은 정적 필드의 지연 초기화 방식으로 `JVM의 클래스 초기화 매커니즘`을 이용해 <br>
스레드 안전성과 성능을 모두 확보하는 방법이다. <br>
`Holder` 클래스는 `필요할 때까지 로드되지 않기 때문에 지연 초기화`가 이루어진다. <br><br>

<br><br>


### 정리하자면
1. `지연 초기화는 반드시 필요한 경우에만 사용` 하는 것이 좋다. <br>
   초기화 비용이 크거나 필드가 자주 사용되지 않을 때 효과적일 수 있다. <br><br>

2. 스레드 안전성을 고려하여 `동기화 전략`을 적절하게 사용하고, 필요 시 `이중 검사 락킹` 또는 `LazyHolder 패턴`과 같은 방법을 활용하는 것이 좋다. <br><br>

3. 지나치게 복잡한 지연 초기화는 `오히려 성능을 저하시킬 수 있으므로` 신중히 적용해야 한다. <br><br>

