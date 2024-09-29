# Item 72. 필요 없는 검사 예외 사용은 피하라

### 내용 요약 <br>
1. 표준 예외 사용의 장점
    - `가독성`
      - 표준 예외를 사용하면 코드를 읽는 사람이 예외가 발생한 상황을 쉽게 이해할 수 있다. <br>
        이미 널리 사용되는 표준 예외는 대부분의 자바 개발자에게 친숙하다. <br><br>

    - `일관성`
      - 새로운 예외를 정의하기보다는 표준 예외를 사용하여 코드의 일관성을 높일 수 있다. <br>
        여러 개발자가 협업하는 환경에서는 통일된 예외 사용이 유지보수에 도움이 된다. <br><br>

    - `불필요한 중복 방지`
      - 표준 예외는 자바 표준 라이브러리에 이미 정의되어 있으므로, 새로운 예외 클래스를 정의하지 않아도 된다. <br>
        이는 코드 중복을 줄이고, 유지보수를 더 쉽게 만든다. <br><br>

2. 주요 표준 예외
    - `IllegalArgumentException`
        - 메소드에 전달된 인수가 부적절할 때 사용한다. <br><br>

    - `IllegalStateException`
        - 객체 상태가 호출된 메소드에 적합하지 않을 때 사용한다. <br><br>

    - `NullPointerException`
        - 널 값을 사용할 수 없는 곳에서 사용될 때 발생하는 예외이다. <br><br>

    - `IndexOutOfBoundsException`
        - 인덱스가 배열, 리스트 또는 문자열에서 허용된 범위를 벗어났을 때 사용한다. <br><br>

    - `ConcurrentModificationException`
        - 컬렉션이 순회 중에 수정되었을 때 발생한다. <br><br>
    
    - `UnsupportedOperationException`
        - 호출된 메소드가 지원되지 않을 때 사용한다. <br><br>

3. 새로운 예외 정의는 신중하게
    - 자바 표준 예외로 처리할 수 없는 `독특한 예외 상황`이 있을 때만 새로운 예외 클래스를 정의해야 한다. <br>
      이때에도 표준 예외를 상속받아 만드는 것이 좋다. <br><br>


4. 표준 예외와 커스텀 예외의 선택
   - 표준 예외가 처리하는 상황을 적절히 반영할 수 있다면 새로운 예외를 만들 필요가 없다. <br>
     하지만, 특별히 상황을 더 명확히 설명해야 하거나, 표준 예외로는 충분히 설명되지 않는 경우에만 새로운 예외를 정의해라. <br><br>

<br><br>




### `IllegalArgumentException` 사용 예시
```java
public class Temperature {
    
    private double value;
    
    public void setTemperature(double value) {
        // 절대 영도 이하의 온도는 물리적으로 불가능
        if (value < -273.15) {
            throw new IllegalArgumentException("온도는 -273.15도 이하일 수 없다.");
        }
        this.value = value;
    }
    
    public double getTemperature() {
        return value;
    }
    
    public static void main(String[] args) {
        Temperature temp = new Temperature();
        
        try {
            temp.setTemperature(-300);
        }
        catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());         // 온도는 -273.15도 이하일 수 없다.
        }
    }
}
```
위 코드에선 `IllegalArgumentException`은 메소드의 인수로 전달된 값이 유효하지 않을 때 던져진다. <br>
여기서는 물리적으로 불가능한 온도를 설정하려는 시도에 대해 예외를 발생시킨다. <br><br>

예외 메세지를 통해 문제를 명확히 설명하여 디버깅을 용이하게 한다. <br><br>

<br><br>



### `IllegalStateException` 사용 예시
```java
public class CoffeeMachine {
    
    private boolean hasWater;
    
    public void addWater() {
        hasWater = true;
    }
    
    public void brew() {
        if(!hasWater) {
            throw new IllegalStateException("물을 넣지 않고 커피를 추출할 수 없다.");
        }
        System.out.println("커피가 추출되었다.");
    }
    
    public static void main(String[] args) {
        CoffeeMachine machine = new CoffeeMachine();
        
        try {
            machine.brew();
        }
        catch (IllegalStateException e) {
            System.out.println(e.getMessage());         // 물을 넣지 않고 커피를 추출할 수 없다.
        }
    }
}
```
위 코드에선 `IllegalStateException`은 객체가 특정 작업을 수행하기에 적합하지 않은 상태일 때, 발생한다. <br>
물이 없는 상태에서 커피를 추출하려는 시도를 차단하고 예외를 던진다. <br><br>

이 방식은 객체의 상태를 명확하게 관리하고 잘못된 사용을 방지하는데 유용하다. <br><br>

<br><br>

### 정리하자면
1. 자바에서 제공하는 `표준 예외`는 잘 설계된 도구이므로, 가능한 경우 이를 사용하여 코드의 `가독성`과 `일관성`을 높이자.
2. 새로운 예외를 정의하기 전에, 표준 예외가 그 상황을 처리할 수 있는지 검토하고, 새로운 예외는 꼭 필요한 경우에만 정의하자
3. `표준 예외`를 잘 활용하면 코드 유지보수가 더 쉬워지고, 다른 개발자와 협업할 때도 더 명확한 의사소통이 가능하다.



