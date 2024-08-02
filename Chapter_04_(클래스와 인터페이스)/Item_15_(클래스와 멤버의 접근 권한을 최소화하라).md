# Item 15. 클래스와 멤버의 접근 권한을 최소화하라

### 내용 요약 <br>
1. `캡슐화` 의 중요성
    - 클래스와 멤버의 접근 권한을 제한함으로써 변경의 영향을 최소화하고 코드 유지보수성을 높인다.
    - 내부 구현을 숨겨 외부에서 수정할 수 없도록 하여 오류 발생을 최소화한다.

<br>

2. 기본 접근 수준
   - 모든 클래스와 멤버에 대해 가장 낮은 접근 권한을 부여한다.
   - 최상위 클래스는 `package-private` 또는 `public`이어야 하며, 다른 모든 클래스는 `private`이어야 한다.

<br>

3. 멤버 접근 수준
   - `public` 멤버는 가능한 한 피해야 한다.
   - 상수 필드는 `public static final`로 선언할 수 있지만, 가변 객체를 포함하지 않도록 주의해야 한다.

<br>


4. 접근자와 변경자
   - `private` 필드를 직접 접근하는 대신 접근자 (getter)와 변경자(setter)를 사용한다.
   - 불변 클래스는 변경자를 제공하지 않는다.

<br>

5. 내부 클래스와 인터페이스
   - 내부 클래스와 인터페이스는 필요한 경우에만 `private`이 아닌 접근 수준으로 선언한다
   - 멤버 클래스는 가능하면 `static`으로 선언하여 외부 클래스의 인스턴스에 대한 암시적 참조를 피한다.


### 접근 권한을 최소화(캡슐화)하여 잘 설계된 클래스 예시
```java
public class Person {
    // 모든 필드는 private으로 선언
    private String name;
    private int age;
    
    // 생성자는 public
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 접근자 메소드 (getter)
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    // 변경자 메소드 (setter)
    public void setName(String name) {
        this.name = name;
    }
    
    public void setAge(int age) {
        if(age > 0) {
            this.age = age;
        } else {
            throw new IllegalArgumentException("Age must be positive");
        }
    }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}
```
위 예시에서 `Person` 클래스는 모든 필드를 `private`으로 선언하고, 접근자 메소드(getter)와 변경자 메소드(setter)를 통해 접근을 제어한다. <br>
이렇게 함으로써 내부 구현을 숨기고 외부에서 직접 변경할 수 없도록 한다. <br>

<br>

### 상수 필드 예시
```java
public class Constants {
    // 가변 객체를 포함하지 않는 상수 필드는 public static final 로 선언 가능
    public static final int DAYS_IN_WEEK = 7;
    public static final String DEFAULT_NAME = "John Doe";
    
    private Constants() {
        // 인스턴스화 방지
    }
}
```
위 예시에서 `Constants` 클래스는 인스턴스화할 수 없도록 설계되었으며, 상수 필드는 `public static final`로 선언되어 외부에서 접근할 수 있지만 변경은 불가능하다. <br>

<br>

### 정리하자면
- 위와 같이 접근 권한 최소화 원칙을 따르면 코드의 유지보수성과 안정성이 크게 향상된다.
