# Item 16. public 클래스에서 public 필드가 아닌 접근자 메소드를 사용하라

### 내용 요약 <br>
1. `public 필드`의 문제점
    - 캡슐화가 깨짐
    - 내부 표현 방식이 외부에 드러나므로 내부 구현을 변경하기 어렵다.
    - 클래스의 불변성을 유지하기 어렵다

<br>

2. 접근자와 변경자의 장점
   - 내부 구현을 숨기고 필요에 따라 변경할 수 있다.
   - 필드에 대한 접근을 제어하고 유효성 검사를 추가할 수 있다
   - 클래스의 불변성을 유지할 수 있다
   - API 유연성이 증가하고 이후 변경이 쉬워진다.

<br>

3. 불변 필드
   - 불변 필드는 `final`로 선언하고 변경자를 제공하지 않는다.
   - 불변 객체는 본질적으로 thread-safe 하며, 공유하기에 적합하다.

<br>

4. 불변 클래스
   - 불변 객체는 본질적으로 스레드에 안전하다.
   - 여러 스레드에서 안전하게 공유가 가능하다.
   - 상태 변경이 없으므로 설계가 단순하다.

<br>


### public 필드를 사용한 클래스 예시
```java
public class Point {
    public int x;
    public int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
위 예시에서 `Point` 클래스가 `public` 필드를 사용한다. 이렇게 하면 외부에서 필드를 직접 수정할 수 있으며 내부구현이 외부에 노출된다. <br>
예를 들어, 좌표를 극좌표로 변경하고 싶다면 기존 코드를 수정하기 어렵다. <br><br>

### 접근자와 변경자를 사용한 클래스 예시
```java
public class Point {
    private int x;
    private int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    // 접근자 메소드(getter)
    public int getX() {
        return x;
    }
    
    public int getY() {
        return y;
    }
    
    // 변경자 메소드 (setter)
    public void setX(int x) {
        this.x = x;
    }
    
    public void setY(int y) {
        this.y = y;
    }
}
```
위 예시에서 `Point`클래스가 `private` 필드를 사용하고, 접근자 메소드와 변경자 메소드를 제공한다. <br>
이렇게하면 내부 구현을 숨기고 필드에 대한 접근을 제어할 수 있다. <br>
또한, 나중에 내부 표현 방식을 변경하더라도 API는 그대로 유지할 수 있다. <br><br>

### 불변 클래스를 사용한 예시
```java
public class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    // 접근자 메소드 (getter)
    public int getX() {
        return x;
    }
    
    public int getY() {
        return y;
    }
    
    // 변경자 메소드가 없음 (불변 클래스)
}
```
위 예시에서 `Point` 클래스가 불변 객체로 설계되었다. 모든 필드는 `final`로 선언되어 있으며, 변경자 메소드는 제공하지 않는다. <br>
이렇게하면 객체의 상태가 생성 후 변경되지 않으므로 thread-safe 를 확보할 수 있다.

<br><br>

### 정리하자면
1. `public 필드`는 피하고 접근자와 변경자를 사용하여 필드에 접근하도록 하자.
2. 캡슐화를 유지하여 내부 구현을 숨기고 필요에 따라 변경할 수 있도록 하자
3. 불변 필드는 final로 선언하고 변경자를 제공하지 않음으로써 불변성을 유지하자
5. 스레드 안정성과 공유를 위해 불변 객체를 사용하는 것이 좋다.


