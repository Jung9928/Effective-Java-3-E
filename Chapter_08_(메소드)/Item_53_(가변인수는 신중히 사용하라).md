# Item 53. 가변인수는 신중히 사용하라

### 내용 요약 <br>
1. `가변인수`란 무엇인가
    - 가변인수 메소드는 동일한 타입의 인수를 여러 개 받을 수 있는 메소드이다. <br>
      이를 위해, 자바는 `...` 문법을 제공하여 메소드가 여러 개의 매개변수를 배열로 처리할 수 있게 한다. <br>
      예를 들어, `int... numbers`는 여러 개의 정수를 받는 매개변수를 의미한다. <br><br>


2. `가변인수`의 성능 문제
    - 가변인수 메소드는 호출될 때마다 배열을 생성하므로, 성능에 영향을 줄 수 있다. <br>
      특히, 가변인수 메소드를 자주 호출하거나 메소드가 매우 자주 실행될 때, 성능 문제는 더욱 부각된다. <br><br>

    - 배열을 생성하는 비용은 작지만, 가변인수를 자주 사용하는 상황에서는 이 비용이 쌓일 수 있으므로 신중하게 사용해야 한다. <br><br>


3. `매개변수가 없는 호출 방지`
    - 가변인수 메소드에서 인수가 아예 전달되지 않는 경우도 허용되므로, 특정 개수 이상의 인수를 반드시 받아야 할 경우엔 이를 명확히 제한해야 한다. <br>
      예를 들어, 최소한 하나 이상의 인수를 요구하는 메소드를 작성할 때, 첫 번째 인수를 고정 매개변수로 설정하고 나머지를 가변인수로 처리하는 방식이 좋다. <br><br>


4. `가변인수 메소드`의 유연성과 안정성
    - 가변인수는 유연한 메소드 호출을 가능하게 하지만, 안전성을 확보하기 위해 잘 설계된 시그니처를 가져야 한다. <br>
      가변인수 메소드가 받을 수 있는 값의 범위나 타입에 대해 충분한 고려가 필요하다. <br><br>

5. `메소드 오버로딩`과 `가변인수`의 혼용
    - 메소드 오버로딩과 가변인수를 함께 사용할 경우, 메소드 호출이 어떻게 처리될 지 예측하기 어려울 수 있다. <br>
      특히, 가변인수와 고정 매개변수를 혼용할 때는 메소드 시그니처가 애매해질 수 있다. <br><br>



### `최소 하나 이상의 인수를 받는 가변인수 메소드`의 잘못된 예시
```java
public class VarargsExample {
    // 가변인수로 인수를 받아 처리
   public static int sum(int... numbers) {
       int sum = 0;
       for(int num : numbers) {
           sum += num;
       }
       return sum;
   }
   
   public static void main(String[] args) {
       System.out.println(sum());               // 0 반환 (매개변수 없이 호출 가능)
   }
}
```
위 코드에서 가변인수 메소드로 인수를 전달하지 않았을 때도 호출이 가능하며, 그 결과는 0이다. <br>
하지만 `sum()` 메소드는 최소한 하나 이상의 인수를 받아야 의미가 있기 때문에, 이 설게는 적절하지 않다. <br><br>


### `최소 하나 이상의 인수를 받는 가변인수 메소드`의 개선된 예시
```java
public class VarargsExample {
    // 첫 번째 인수는 고정, 나머지는 가변인수로 처리
   public static int sum(int firstNumber, int... numbers) {
       int sum = firstNumber;
       for(int num : remainingNumbers) {
           sum += num;
       }
       return sum;
   }
   
   public static void main(String[] args) {
       System.out.println(sum(1, 2, 3));                // 6 반환
       System.out.println(sum(5));                // 5 반환 (매개변수 하나만 제공)
   }
}
```
위 코드에서 `sum`메소드는 첫 번째 인수를 고정 매개변수로 받고, 그 이후의 인수는 가변인수로 처리한다. <br>
이를 통해, 최소한 하나 이상의 인수를 반드시 받아야 한다는 요구사항을 명확하게 강제할 수 있다. <br><br>


### 성능 문제 해결을 위한 가변인수 사용 예시
```java
public class VarargsExample {
    // 가변인수로 여러 문자열을 결합
   public static String concatenate(String first, String... others) {
       StringBuilder sb = new StringBuilder(first);
       for(String other : others) {
           result.append(other);
       }
       return result.toString();
   }
   
   public static void main(String[] args) {
       System.out.println(concatenate("Hello", " ", "world", "!"));                // "Hello world!"
       System.out.println(concatenate("Java"));                                    // "Java"
   }
}
```
위 코드에서 `concatenate` 메소드는 최소한 하나 이상의 문자열을 받아 결합한다. <br>
`StringBuilder`를 사용해 성능을 최적화하면서, 가변인수를 효율적으로 처리했다. <br>
첫 번째 인수는 고정 매개변수로 처리하여, 인수가 없는 호출을 방지하고 불필요한 배열 생성을 줄인다. <br><br>






