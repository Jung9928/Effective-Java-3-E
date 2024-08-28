# Item 44. 표준 함수형 인터페이스를 사용하라

### 내용 요약 <br>
1. `함수형 인터페이스`란?
    - 단 하나의 추상 메소드를 가지는 인터페이스 
    - 람다 표현식이나 메소드 참조와 함께 사용.
    - 자바 8부터 도입된 개념이며, 코드 간결성과 함수형 프로그래밍 스타일을 장려. <br><br>

2. `표준 함수형 인터페이스`
   - 자바 표준 라이브러리(java.util.function)는 다양한 표준 함수형 인터페이스를 제공한다. <br>
     이를 사용하면 코드의 일관성을 유지하고, 재사용성과 가독성을 높일 수 있다. <br><br>
   
   - 대표적인 표준 함수형 인터페이스는 다음과 같다. 
      - `Function<T, R>` : T를 입력받아 R을 반환하는 함수
      - `Predicate<T>` : T를 입력받아 boolean을 반환하는 함수
      - `Consumer<T>` : T를 입력받아 소비(반환값이 없는) 하는 함수.
      - `Supplier<T>` : 입력은 없고, T를 반환하는 함수
      - `UnaryOperator<T>` : T를 입력받아 동일한 T타입을 반환하는 함수 (`Function<T, T>`와 동일).
      - `BinaryOperator<T>` : 동일한 타입 T의 두 인수를 받아 T를 반환하는 함수
        (`BiFunction<T, T, T>`와 동일)


3. 표준 함수형 인터페이스 사용의 장점
   - 재사용성 
     - 표준 인터페이스를 사용하면 다양한 컨텍스트에서 코드 재사용이 쉬워진다.
     
   - 일관성
     - 자바 라이브러리와의 호환성을 유지할 수 있고 표준화된 방식으로 코드 작성이 가능하다.
   
   - 가독성
     - 이미 널리 알려진 인터페이스이므로, 코드의 의도를 명확히 전달할 수 있어 가독성이 향상된다.
   
   - 코드 중복 방지
     - 표준 인터페이스를 사용하면 불필요한 코드 중복을 줄일 수 있다.
   

4. 사용자 정의 함수형 인터페이스는 신중하게
   - 표준 인터페이스로 처리할 수 없는 특별한 경우에만 사용자 정의 인터페이스를 만들 것을 권장한다. <br>
     일반적인 경우에는 표준 인터페이스를 사용하는 것이 좋다. <br><br>


<br><br>

### `표준 함수형 인터페이스`를 사용한 경우
```java
import java.util.function.*;

public class FunctionExample {
    
   public static void main(String[] args) {
       
       // Function<T, R> 사용 : String을 Integer로 변환
       Function<String, Integer> stringIntegerFunction = String::length;
       System.out.println("Length of 'apple' : " + stringIntegerFunction.apply("apple"));
       
      // Predicate<T> 사용 : 문자열의 길이가 5 이상인지 확인
      Predicate<String> isLongerThanFive = str -> str.length() > 5;
      System.out.println("'banana' is longer than five : " + isLongerThanFive.test("banana"));
   }
}
```
위 코드에서 `Function<String, Integer>` 인터페이스는 문자열의 길이를 계산하는데 사용되었다. <br>
표준 인터페이스를 사용하므로 코드가 명확하고 재사용 가능하다. <br><br>

문자열 길이가 5보다 큰 지를 판단하는 람다 표현식을 `Predicate<String>`을 사용해 정의했다. <br>
`Predicate`는 `test` 메소드를 통해 boolean 값을 반환한다. <br><br>


### `사용자 정의 인터페이스`를 사용하지 않은 경우
```java
import java.util.function.*;

public class FunctionExample {
    
   public static void main(String[] args) {
       
       // BiFunction<T, U, R> 사용 : 두 Integer를 더하여 Integer로 반환
       BiFunction<Integer, Integer, Integer> adder = (a, b) -> a + b;
       System.out.println("Sum of 3 and 5 : " + adder.apply(3, 5));
   }
}
```
위 코드에서 `BiFunction<Integer, Integer, Integer>`를 사용해 두 정수를 더하는 함수를 정의했다. <br>
기존의 `Adder` 인터페이스를 정의하는 대신 표준 인터페이스를 사용하여 코드가 간결하고 명확해졌다. <br><br>

<br><br>

