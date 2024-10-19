# Item 01. 생성자 대신 정적 팩토리 메소드를 고려하라

### 내용 요약 <br>
1. 이름을 가질 수 있다
    - 생성자와 달리 `정적 팩토리 메소드`는 의미 있는 이름을 가질 수 있다. <br>
      이를 통해 메소드의 역할을 보다 명확하게 전달할 수 있다. <br><br>

    - 예를 들어, `BigInteger(int, int)` 생성자 대신 `BigInteger.probablePrime(int, Random)`과 같은 이름을 사용하면 의도가 명확히 전달된다. <br><br>


2. 호출될 때마다 새로운 객체를 만들 필요가 없다
    - 생성자는 호출할 때마다 새로운 객체를 생성하지만, `정적 팩토리 메소드`는 호출 시 이미 생성된 객체를 반환하거나 캐시된 인스턴스를 재사용할 수 있다. <br><br>

    - 이를 통해 `불필요한 객체 생성을 피하고, 성능 최적화`가 가능하다. <br><br>

3. 반환 타입의 하위 타입 객체를 반환할 수 있다
    - 정적 팩토리 메소드는 반환 타입으로 하위 클래스의 객체를 반환할 수 있다. <br>
      이를 통해, `인터페이스 기반 설계`에서 유연성을 높일 수 있다. <br><br>

    - 예를 들어, `Collection` 인터페이스를 반환하면서 실제로 `List`나 `Set` 객체를 반환하는 식으로 다양한 구현을 선택할 수 있다. <br><br>

4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다
    - 정적 팩토리 메소드는 매개변수에 따라 `다른 클래스의 인스턴스를 반환`할 수 있다. <br>
      이는 `다형성을 극대화`하고 코드의 유연성을 높여준다. <br><br>

5. 객체 생성과 관련된 문맥 정보를 캡슐화할 수 있다
   - 정적 팩토리 메소드를 사용하면 `객체 생성 로직을 캡슐화`하여 클라이언트 코드가 세부 구현에 의존하지 않게 할 수 있다. <br>
     이를 통해, 코드의 유지보수성과 테스트 용이성을 높일 수 있다. <br><br>


7. 하위 클래스를 반환할 수 있어 생성자와 달리 코드가 덜 제한적이다
   - 정적 팩토리 메소드는 반환 타입의 하위 클래스 인스턴스를 반환할 수 있다. <br>
     이를 통해, `확장 가능성`이 커진다. <br><br>

8. 정적팩토리 메소드의 단점
   - 상속을 지원하지 않는다
     - 정적 메소드는 상속할 수 없기 때문에, 정적 팩토리 메소드를 제공하는 클래스는 보통 `상속을 제한`하게 된다. <br><br>

   - API에서 쉽게 눈에 띄지 않는다
     - 생성자는 클래스 선언에 명시되어 있어 API 문서에서 바로 확인할 수 있지만, 정적 팩토리 메소드는 그렇지 않다. <br>
       이를 보완하기 위해 잘 알려진 `네이밍 컨벤션`을 따르는 것이 중요하다. <br><br>

9. 네이밍 컨벤션
   - `from` : 매개변수를 사용해 하나의 인스턴스를 반환
   - `of` : 여러 매개변수를 받아 적합한 인스턴스를 반환
   - `valueOf` : `from`과 비슷하나 더 직관적인 이름
   - `instance` 또는 `getInsntace` : 인스턴스를 반환하되, 호출마다 새로운 인스턴스를 생성하지 않을 수 있음
   - `create` : 호출할 때마다 새로운 인스턴스를 반환
   - `newInstance` : `create`와 같으나, 호출할 때마다 새로운 인스턴스를 확실히 반환
   - `getType` : `getInstance`와 비슷하나, 반환 타입을 강조
   - `newType` : `newInstance`와 같으나, 반환 타입을 강조


<br><br>


### 생성자를 사용하는 경우
```java
import java.io.*;

public class Boolean {
   
    private final boolean value;
    
    public Employee(boolean value) {
        this.value = value;
    }
    
    public static Boolean valueOf(boolean value) {
        return value ? Boolean.TRUE : Boolean.FALSE;
    }
}
```
위 코드에서 `Boolean` 클래스는 정적 팩토리 메소드 `valueOf`를 제공하여 객체를 생성한다. <br>
`valueOf` 메소드는 기존의 상수 객체인 `Boolean.TRUE` 또는 `Boolean.FALSE`를 반환하며, 새로운 객체를 생성하지 않는다. <br>
이 방법을 통해 `불필요한 객체 생성을 방지`한다. 

<br><br>


### 정적 팩토리 메소드를 사용하는 경우
```java
import java.io.*;

public class Person {
   
    private final String name;
    private final int age;
    
    private Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 정적 팩토리 메소드
    public static Person of(String name, int age) {
        return new Person(name, age);
    }
    
    @Override
    public String toString() {
        return "Person{name = '" + name + "', age = " + age + "}";
    }
}
```
위 코드에서 `Person` 클래스는 생성자를 `private`으로 감추고, 대신 정적 팩토리 메소드 `of`를 제공한다. <br>
`of` 메소드를 통해 객체를 생성하므로, `코드가 더 명확해지고 유연성`을 가질 수 있다. <br>
이는 이름을 통해 메소드의 의미를 명확히 전달한다

<br><br>



### 정적 팩토리 메소드로 객체 캐싱 구현
```java
import java.io.*;

public class Color {
   
    private static final Map<String, Color> colorCache = new HashMap<>();
   
    private final String name;
    
    private Color(String name) {
        this.name = name;
    }
    
    // 정적 팩토리 메소드
    public static Color valueOf(String name) {
        return colorCache.computeIfAbsent(name, Color::new);
    }
}
```
위 코드에서 `Color` 클래스는 `valueOf` 정적 팩토리 메소드를 사용하여 `객체 캐싱`을 구현한다. <br>
`colorCache`에서 요청된 색상이 없으면 새로운 객체를 생성하고, 이미 존재하면 캐시된 객체를 반환한다. <br>
이를 통해 `불필요한 객체 생성`을 줄이고 `메모리 사용을 최적화`할 수 있다. <br><br>

<br><br>


### 정리하자면
1. 정적 팩토리 메소드는 `더 유연한 객체 생성 방식`을 제공하며, 이름을 가질 수 있고 객체 생성 시 다양한 이점을 누릴 수 있다. 
2. 생성자보다 `유연한 반환 타입`을 제공할 수 있고 `불필요한 객체 생성을 줄일 수 있는` 가능성을 제공하며 객체를 `캐싱`할 수 있는 장점을 가지고 있다
3. 하지만 상속을 허용하지 않기 때문에 이를 사용할 때는 `상속을 고려해야`한다
