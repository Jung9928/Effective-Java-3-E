# Item 69. 예외는 진짜 예외 상황에만 사용하라

### 내용 요약 <br>
1. 예외의 본래 용도
    - 예외는 비정상적이고 예측 불가능한 상황에서만 사용해야 한다. <br>
      정상적인 흐름 제어를 위해 예외를 사용하면 `코드의 성능`이 저하되고 `읽기 어려워`진다. <br><br>

2. 일반적인 제어 흐름에 예외를 사용하지 마라
    - 일반적인 프로그램 흐름에서는 조건문, 반복문, 반환값 등을 사용해야 하며, `예외는 비정상적인 상황에서만` 발생해야 한다. <br>
      예외를 남용하면 프로그램이 `비효율적`으로 동작하게 된다. <br><br>


3. 비용이 많이 드는 예외 처리
    - 예외는 발생할 때마다 `스택 트레이스`를 생성하고 이를 분석하므로, `비용이 크고 성능에 악영향`을 줄 수 있다. <br><br>


4. 대안적인 해결 방법
    - 정상적인 프로그램 흐름에서는 `if-else`나 `switch`문을 사용하여 조건을 처리하는 것이 더 적합하다. <br>
      예외는 정말로 예측하지 못한 오류나 `비정상적인 상태`에서만 사용하도록 제한해야 한다. <br><br>

5. 예외 처리의 의미를 명확하게 유지하라
    - 예외가 발생한다는 것은 프로그램이 비정상적인 상태에 있다는 의미이므로, 이 경우에만 예외를 사용하는 것이 좋다. <br>
      예외가 자주 발생하면 프로그램에 `문제가 있다는 신호`일 수 있다. <br><br>

<br><br>



### 잘못된 예외 사용 예시
```java
public class ExceptionExample {

    private static final List<String> validOptions = Arrays.asList("A", "B", "C");
    
    public static void processOption(String option) {
        try {
            int index = validOptions.indexOf(option);
            
            if(index == -1) {
                throw new IllegalArgumentException("Invalid option : " + option);
            }
            
            System.out.println("Processing option : " + option);
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}
```
위 코드에선 `정상적인 흐름 제어`를 위해 예외를 사용하고 있다. <br>
`indexOf`메소드를 통해 옵션이 리스트에 없으면 예외를 던지는데, 이는 조건문으로 처리하는 것이 더 적합하다. <br><br>

예외는 발생할 때마다 성능 저하를 일으키며, 단순한 흐름 제어에 예외를 사용하는 것은 코드의 `가독성`도 떨어뜨린다. <br><br>

<br><br>


### 올바른 예외 사용 예시
```java
public class ExceptionExample {

    private static final List<String> validOptions = Arrays.asList("A", "B", "C");
    
    public static void processOption(String option) {
        if(!validOptions.contains(option)) {
            System.out.println("Invalid option : " + option);
            return;
        }
        System.out.println("Processing option : " + option);
    }
}
```
위 코드에선 `조건문`을 사용하여 예외적인 상황을 처리하지 않고, `정상적인 흐름`에서 올바르게 조건을 검사하고 있다. <br><br>
`예외는 발생하지 않으며` 코드가 더 `간결하고 효율적`이다.

<br><br>



### 예외를 사용해야 하는 경우의 예시
```java
public class ExceptionExample {

    public static int divide(int numerator, int denominator) {
        if(denominator == 0) {
            throw new ArithmeticException("Division by zero is not allowed");
        }
        
        return numerator / denominator;
    }
}
```
위 코드에선 `정말로 비정상적인 상황`인 0으로 나누기를 처리하기 위해 예외를 사용하는 것이 적합하다. <br>
0으로 나누는 것은 일반적인 프로그램 흐름에서 발생해서는 안되므로, 이 경우 예외를 사용하는 것이 올바른 사용 방법이다. <br><br>



### 정리하자면
1. 예외는 `진짜 예외적인 상황`에서만 사용해야 하며, 일반적인 흐름 제어에 사용하는 것은 `비효율적이고 `잘못된 사용법`이다.
2. 조건문을 사용하여 `정상적인 흐름`을 처리하고, 예외는 비정상적인 상황에서만 발생하게 해야 한다.
3. 올바르게 예외를 사용함으로써 프로그램의 `성능`과 `가독성`을 높이고, 예외가 발생했을 때, 의미를 명확하게 전달할 수 있다. <br>
