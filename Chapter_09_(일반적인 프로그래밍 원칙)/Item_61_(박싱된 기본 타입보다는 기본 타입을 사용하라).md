# Item 61. 박싱된 기본 타입보다는 기본 타입을 사용하라

### 내용 요약 <br>
1. `기본 타입 vs 박싱된 타입`
    - `기본 타입`은 `int`, `boolean`, `double`과 같이 값만을 담는 타입이며, 효율적이고 메모리 사용이 적다. <br><br>

    - `박싱된 타입`은 `Integer`, `Boolean`, `Double`과 같이 객체로서 값을 담고 있으며, 추가적인 메모리와 성능 오버헤드가 발생한다. <br><br>


2. 성능 문제
    - 박싱된 타입은 기본 타입보다 `메모리 사용량이 많고` 연산 시, 성능이 저하된다. <br>
      박싱과 언박싱이 자동으로 이루어지지만 이로 인해 성능 저하와 `불필요한 객체 생성`이 발생할 수 있다. <br><br>
    

3. 동등성 문제
    - 기본 타입은 `==`연산자를 사용해 값을 비교할 수 있지만, 박싱된 타입은 `객체 참조를 비교`한다. <br>
      이로 인해, 예기치 않은 버그가 발생할 수 있다. <br><br>


4. Null 처리 문제
    - 기본 타입은 null 값을 가질 수 없지만 박싱된 타입은 `null`값을 가질 수 있다. <br>
      이로 인해, null 포인터 예외가 발생할 가능성이 있다. <br><br>

5. 결론
   - 기본 타입을 우선 사용하고, `필요할 때만 박싱된 타입`을 사용하는 것이 좋다. <br>
     특히, `컬렉션`과 같은 상황에서는 박싱된 타입이 필요할 수 있지만, 그 외의 경우에는 기본 타입을 사용하는 것이 더 효율적이다. <br><br>

<br><br>


### 잘못된 예시 : 박싱된 타입을 사용하여 발생하는 문제
```java
public class Item61Example {
    
    public static void main(String[] args) {
        
        // 박싱된 타입 사용
        Integer a = 1000;
        Integer b = 1000;
        
        // == 연산자 사용으로 인해 객체 참조를 비교함
        if (a == b) {
            System.out.println("a와 b는 같다.");                // 출력되지 않음
        }
        else {
            System.out.println("a와 b는 다르다.");               // 출력됨
        }
        
        // NullPointerException 발생 가능성
        Integer c = null;
        int d = c;                                              // 언박싱 과정에서 NullPointerException 발생
    }
}
```
위 코드에서 `a == b`는 박싱된 타입인 `Integer`끼리의 비교로, 객체 참조를 비교하게 된다. <br>
두 객체는 동일하지 않으므로 `false`를 반환하며, 값 비교가 아니라 참조 비교로 인해 예기치 않은 결과를 초래한다. <br><br>

또한, `c`가 `null`인 상황에서 언박싱 할 때, `NullPointerException`이 발생한다.

<br><br>




### 올바른 예시 : 기본 타입을 사용하여 성능과 오류 방지
```java
public class Item61Example {
    
    public static void main(String[] args) {
        
        // 기본 타입 사용
        int a = 1000;
        int b = 1000;
        
        // 값 비교가 정확히 이루어짐
        if (a == b) {
            System.out.println("a와 b는 같다.");                // 출력됨
        }
        
        // NullPointerException 발생 가능성 없음
        int c = 0;                                              // 기본 타입은 null일 수 없음
        System.out.println(c);
    }
}
```
위 코드에서 기본 타입인 `int`를 사용하여 값 비교가 정확하게 이루어진다. <br>
또한, 기본 타입은 `null`을 허용하지 않으므로 `NullPointerException`을 피할 수 있다. <br><br>

이 방식은 메모리 효율성과 성능 면에서도 더 뛰어나다. 

<br><br>



### 예시 : 컬렉션에서 박싱된 타입 사용
```java
public class Item61Example {
    
    public static void main(String[] args) {
        
        // 컬렉션에서는 박싱된 타입을 사용해야 함
        Map<Integer, String> map = new HashMap<>();
        map.put(1, "One");
        map.put(2, "Two");
        
        // 박싱된 타입을 사용하는 상황에서는 어쩔 수 없이 사용
        Integer key = 1;
        System.out.println(map.get(key));               // "One" 출력
    }
}
```
컬렉션(`Map`, `List` 등)에서는 기본 타입을 사용할 수 없으므로, `박싱된 타입`을 사용해야 한다. <br>
위 코드에서는 박싱된 타입의 사용이 불가피하다. 

<br><br>



# 정리하자면
1. 가능한 한 `기본 타입`을 사용하고, 박싱된 타입은 필요한 경우에만 사용해야 한다. 
2. `기본 타입`을 사용하면 성능이 향상되고 불필요한 오류를 방지할 수 있다.
3. 특히, `null`처리나 값 비교에서는 기본 타입을 사용하여 오류를 줄일 수 있다. 