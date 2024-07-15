# Item 9. try-finally 보다는 try-with-resources를 사용하라

### 내용 요약 <br>
equals 메소드는 재정의하기 쉬워보이지만 정확히 알고 사용하지 않으면 예기치 못한 상황에 맞닥뜨릴 수 있다. <br>
문제를 발생시키지 않는 제일 좋은 방법은 되도록이면 '재정의'를 하지 않는 것이다. <br>

그러므로 다음에서 열거한 상황 중 하나에 해당한다면 재정의 하지 않는 것이 최선이다. <br>

- #### 각 인스턴스가 본질적으로 고유하다
  - 값 표현이 아니라 동작하는 개체를 표현하는 클래스가 여기에 해당된다. Thread가 좋은 예로, Object의 equals 메소드는 이러한 클래스에 맞게 구현되었다. <br><br>

- #### 인스턴스의 `논리성 동치성(logical equality)`을 검사할 일이 없다.
  - 예를들어, java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지를 검사하는 <br>
    '논리적 동치성'을 검사하는 방법도 있으나, 클라이언트에서 이 방식을 사용하지 않을 경우, Object의 기본 equals만으로도 해결된다. <br><br>

- #### 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다. <br>
  - 예를들어, 대부분의 `Set` 구현체는 `AbstractSet`이 구현한 equals를 상속받아 쓰고, `List` 구현체들은 `AbstractList`로부터, <br>
    `Map` 구현체들은 `AbstractMap`으로부터 상속받아 그래도 쓴다. <br><br>

- #### 클래스가 private이거나 package-private이고 equals 메소드를 호출할 일이 없다. <br>
  - 예를 들어, 위험을 철저하게 피하고 싶어서 equals가 실수로라도 호출되는 것을 막고 싶다면 다음처럼 구현하자. <br>

```java
@Override
public boolean equals(Object o) {
    throw new AssertionError();         // 호출 금지!    
}
```

<br>

`equals` 메소드를 재정의해야 한다면 아래의 규약을 준수해야 한다. <br>

1. 반사성(reflexivity) 
   - 객체는 자기 자신과 같아야 한다. `x.equals(x)`는 항상 `true`를 반환해야 한다. <br><br>

2. 대칭성(symmetry)
   - 두 객체가 서로 같아야 한다면, 그 반대도 성립해야 한다. `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`여야 한다. <br><br>
   
3. 추이성(transitivity)
   - 첫 번째 객체가 두 번째 객체와 같고, 두 번째 객체가 세 번째 객체와 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다. <br>
     `x.equals(y)`가 `true`이고 `y.equals(z)`가 `true`이면 `x.equals(z)`도 `true`여야 한다. <br><br>

4. 일관성(consistency)
   - 두 객체가 같다면, 그 상태가 변하지 않는 한 여러 번 호출해도 결과는 항상 같아야 한다. <br>
     즉, `x.equals(y)`가 반복 호출 시, 항상 `true`또는 `false`를 반환해야 한다. <br><br>

5. null-아님(non-nullity)
   - 모든 객체는 `null`과 같지 않아야 한다. `x.equals(null)`은 항상 `false`여야 한다. <br><br>


### 잘못된 equals 재정의 예시 코드
```java
public class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    public boolean equals(Object o) {
        if (o == this) 
            return true;
        
        if (!(o instanceof Point)) 
            return false;
        
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
    
    // hashCode 메소드 생략 (equals를 재정의 할 땐 hashCode도 재정의해야 함)
}
```
위 예시에서는 `equals` 메소드가 제대로 재정의된 것처럼 보이지마 `hashCode`메소드를 재정의 하지 않았기 때문에 문제가 발생할 수 있다. 

<br><br>

### 올바른 equals 재정의 예시
```java
public class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    public boolean equals(Object o) {
        if(o == this) 
            return true;        // 반사성
      
        if(!(o instanceof Point))
            return false;       // null 검사 및 타입 검사
       
        Point p = (Point) o;
        
        return p.x == x && p.y == y;        // 대칭성, 추이성, 일관성
    }
    
    @Override
    public int hashCode() {
        return 31 * x + y;
    }
    
    public static void main(String[] args) {
        Point p1 = new Point(1, 2);
        Point p2 = new Point(1, 2);
        Point p3 = new Point(1, 3);
        
        // equals 테스트
        System.out.println(p1.equals(p2));                                          // true
        System.out.println(p1.equals(p3));                                          // false
        System.out.println(p1.equals(null));                                        // false
      
        // 대칭성 테스트
        System.out.println(p1.equals(p2) == p2.equals(p1));                         // true
      
        // 추이성 테스트
        Point p4 = new Point(1, 2);
        System.out.println(p1.equals(p2) && p2.equals(p4) && p1.equals(p4));        // true
      
        // 일관성 테스트
        System.out.println(p1.equals(p2));                                          // true
        
        // hashCode 테스트
        System.out.println(p1.hashCode() == p2.hashCode());                         // true
    }
  
}
```
위 예시에서는 `equals` 메소드와 `hashCode` 메소드를 올바르게 재정의했다. 이를 통해 다음을 보장할 수 있다. <br>
1) `equals` 규약 준수
2) `hashCode` 메소드와의 일관성 유지

위 예시 코드를 설명하자면, <br>
- 반사성 : `o`가 `this`와 같은 객체일 경우, 항상 `true`를 반환한다.
- 대칭성 : `o`가 `Point` 타입인지 검사하고 필드값을 비교하여 대칭성을 보장한다.
- 추이성 : 동일한 필드 값을 가진 객체들이 같은 객체로 인식된다.
- 일관성 : 객체의 상태가 변하지 않는 한 `equals` 메소드는 항상 일관된 결과를 반환한다.
- null-아님 : `o`가 `Point` 인스턴스가 아니면 `false`를 반환한다.


`hashCode` 메소드를 재정의하여 `equals`가 `true`인 객체들은 동일한 `hashCode`를 가지도록 한다. <br>
이 개념은 해시 기반 컬렉션(`HashMap`, `HashSet` 등)에서 올바르게 동작하기 위해 필수적인 개념이다.

<br><br>

### 마지막으로 정리하자면
1) `equals` 메소드를 재정의할 때는 반사성, 대칭성, 추이성, 일관성, non-null 규약을 준수해야 한다.
2) `hashCode` 메소드도 함께 재정의하여 `equals` 메소드와 일관성을 유지해야 한다.
3) 올바른 재정의는 코드의 신뢰성과 유지보수성을 높여준다.

