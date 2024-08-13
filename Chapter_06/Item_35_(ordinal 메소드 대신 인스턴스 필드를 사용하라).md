# Item 35. ordinal 메소드 대신 인스턴스 필드를 사용하라

### 내용 요약 <br>
1. `ordinal()`메소드 사용의 문제점
    - 열거형 상수가 정의된 순서를 나타내는 정수값을 반환한다. <br> 
      하지만, 열거형의 순서가 변경되면 `ordinal()`의 값도 바뀌어 의도하지 않은 동작을 초래할 수 있다. <br><br>
      
    - 코드의 가독성 및 유지보수성이 떨어지고, 열거형 상수의 의미를 파악하기 어렵다. <br><br>

    - 타입 안전을 보장할 방법이 없다. <br><br><br>

2. `인스턴스 필드`의 장점
    - 명확한 의미
        - `인스턴스` 필드는 상수의 의미를 명확하게 정의할 수 있어 코드의 가독성을 향상시킨다. <br><br>

    - 안정성
        - 필드 값은 열거형 상수가 변경되더라도 변하지 않으므로 안정성을 보장한다. <br><br>

    - 확장성
        - 새로운 열거형 상수를 추가하더라도 기존 코드를 변경하지 않고 필드를 통해 정보를 관리할 수 있다. <br><br>


### `ordinal()` 메소드를 사용하는 경우
```java
public enum Planet {

   MERCURY(3.303e+23, 2.4397e6),
   VENUS(4.869e+24, 6.0518e6),
   EARTH(5.976e+24, 6.37814e6),
   MARS(6.421e+23, 3.3972e6),
   JUPITER(1.9e+27, 6.211e7),
   SATURN(5.688e+26, 5.723e7),
   URANUS(8.686e+25, 2.536e7),
   NEPTUNE(1.024e+26, 2.462e7);
   
   private final double mass;               // 킬로그램
   private final double radius;             // 미터
    
   Planet(double mass, double radius) {
       this.mass = mass;
       this.radius = radius;
   }
   
   public double surfaceGravity() {
       final double G = 6.67300E-11;            // 글로벌 중력 상수
       return G * mass / (radius * radius);
   }
   
   public double surfaceWeight(double otherMass) {
       return otherMass * surfaceGravity();
   }
   
   // ordinal() 메소드를 사용하는 경우
   public int getOrder() {
       return getOrder();
   }
}
```
`ordinal()`메소드는 열거형 상수가 정의된 순서에 기반한 정수 값을 반환한다. 이는 상수의 의미와 무관하며, 순서가 변경되면 값도 바뀔 수 있다. <br><br>

<br><br>



### `인스턴스 필드`를 사용하는 경우
```java
public enum Planet {

   MERCURY(3.303e+23, 2.4397e6),
   VENUS(4.869e+24, 6.0518e6),
   EARTH(5.976e+24, 6.37814e6),
   MARS(6.421e+23, 3.3972e6),
   JUPITER(1.9e+27, 6.211e7),
   SATURN(5.688e+26, 5.723e7),
   URANUS(8.686e+25, 2.536e7),
   NEPTUNE(1.024e+26, 2.462e7);
   
   private final double mass;               // 킬로그램
   private final double radius;             // 미터
   private final int order;
    
   Planet(double mass, double radius, int order) {
       this.mass = mass;
       this.radius = radius;
       this.order = order;
   }
   
   public double surfaceGravity() {
       final double G = 6.67300E-11;            // 글로벌 중력 상수
       return G * mass / (radius * radius);
   }
   
   public double surfaceWeight(double otherMass) {
       return otherMass * surfaceGravity();
   }
   
   // ordinal() 메소드를 사용하는 경우
   public int getOrder() {
       return getOrder();
   }
}
```
`order` 필드를 사용하여 각 열거형 상수의 고유한 순서 정보를 명확히 지정한다. <br>
이는 열거형 상수가 추가되거나 순서가 변경되더라도 필드 값은 일정하게 유지되므로 안정적이다. <br><br>

<br><br>