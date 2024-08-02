# Item 6. 불필요한 객체 생성을 피하라

- 내용 요약 : 인스턴스를 반복 생성 시, 성능 오버헤드가 발생하므로 <br>
             같은 기능의 객체를 불필요하게 매번 생성하지말고 객체 하나만 생성해서 재사용을 하는게 낫다.
<br><br>
- 방    법 <br>
    1) 불변 객체 재사용 : 불변 객체는 재사용할 수 있기 때문에 매번 새로운 객체를 생성하는 대신 기존 객체를 재사용하자. <br><br>
    2) 정적 팩토리 메소드 사용 : `Boolean.valueOf()`와 같이 정적 팩토리 메소드를 사용하여 새 인스턴스 생성 대신 기존 인스턴스를 재사용하자. <br><br>
    3) 상수 사용 : 상수를 활용하여 반복적으로 사용되는 개체를 미리 생성해놓고 재사용하자.
    4) 오토 박싱 피하기 : 기본(Primitive) 타입을 객체로 변환하는 과정(오토박싱)을 피하고, 기본 타입을 직접 사용하는 것이 성능에 좋다.

<br>

### ex) 사용하지 말아야 할 예시코드
```Java
String s = new String("bikini");        // 이렇게 작성하지 말 것
```
위 코드는 실행 시 마다 String 인스턴스를 새로 생성한다. -> Heap 낭비

### ex) 개선된 코드
```Java
String s = "bikini";
```

이 코드는 실행 시 마다 새로운 인스턴스 생성 대신 하나의 String 인스턴스를 사용한다. <br>
JVM 내에서 똑같은 문자열 상수 값 (위 코드에선 bikini) 을 사용하는 모든 코드가 같은 객체를 재사용하도록 보장한다. <br>

<br>

### ex) 불필요한 객체 생성 코드 예시 
```Java
public class StringExample {
    public static void main(String[] args) {
        
        // 반복적으로 새로운 String 객체 생성
        String s1 = new String("example");
        String s2 = new String("example");
        
        // s1과 s2는 서로 다른 인스턴스
        System.out.println(s1 == s2);       // false
    }
}
```
<br><br>

### ex) 불필요한 객체 생성을 피하는 코드 예시
```Java
public class StringExample {
    public static void main(String[] args) {
        
        // 문자열 상수 사용 (불변 객체 재사용)
        String s1 = "example";
        String s2 = "example";
        
        // s1과 s2는 같은 객체 참조
        System.out.println(s1 == s2);       // true
      
        // 정적 팩토리 메소드 사용 (불변 객체 재사용)
        Boolean b1 = Boolean.valueOf(true);
        Boolean b2 = Boolean.valueOf(true);
        
        // b1과 b2는 같은 객체 참조
        System.out.println(b1 == b2);       // true
      
        // 오토 박싱 (아래 코드는 사용을 지양함)
        Integer i1 = 1000;                  // 오토 박싱 (불필요한 객체 생성)
        Integer i2 = 1000;                  // 오토 박싱 (불필요한 객체 생성)
      
        // i1과 i2는 서로 다른 객체
        System.out.println(i1 == i2);       // false
      
        // 기본 타입 사용
        int i3 = 1000;
        int i4 = 1000;
        
        // i3와 i4는 같은 값을 가짐
        System.ouit.println(i3 == i4);      // true
    }
}
```

위 코드에서 정적 팩토리 메소드인 valueOf()를 사용하여 `Boolean` 객체를 재사용하고 <br>
문자열 상수를 사용하여 `String` 객체를 재사용하며, 기본 타입인 int를 사용하여 불필요한 객체 생성을 피함으로써 <br>
성능 최적화와 메모리 사용 최소화가 적용됨. <br><br>


### Boolean.valueOf 정적 팩토리 메소드
- 아래는 `Boolean.valueOf` 메소드의 내부 로직 일부임. <br>
  실제 구현은 `java.lang.Boolean` 참고

```Java
public final class Boolean implements java.io.Serializable, Comparable<Boolean> {
    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);

    private final boolean value;

    // Private constructor to ensure no other Boolean instances are created
    private Boolean(boolean value) {
        this.value = value;
    }
    
    public static Boolean valueOf(boolean b) {
        return b ? TRUE : FALSE;
    }

    // Other methods...
}
```
위 코드를 보면 `return b ? TRUE : FALSE;` 이부분에서 불필요한 객체 생성을 피하고 이미 생성된 상수를 반환함으로써<br>
메모리 사용을 최적화하는 것을 알 수 있다.

<br>

### ex) 성능에 문제가 있는 정규표현식 코드
```Java
// 주어진 문자열이 유효한 로마숫자인지 확인하는 메소드
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3}"
        + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3}$");
}
```
위 코드의 문제는 String.matches 메소드를 사용한다는데 있음. <br>
정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만 성능 측면에서는 비효율적임. <br>
matches() 메소드 내부에서 정규표현식용 Pattern 인스턴스는 한 번만 사용된 후, 버려져서 GC 대상이 된다. <br>
Pattern 인스턴스는 입력받은 정규표현식에 대한 유한상태머신을 생성하므로 인스턴스 생성 오버헤드가 크다. <br><br>

성능을 개선하려면 필요한 정규표현식을 표현하는 불변인 Pattern 인스턴스를 클래스 초기화(정적 초기화) 과정에서 <br>
직접 생성 후, 캐싱처리하고 나중에 Pattern 인스턴스의 matches() 메소드가 재호출 될 때, 이전에 생성한 Pattern 인스턴스를 <br>
재사용 한다.


<br><br>

### ex) 개선된 정규표현식 코드
```Java
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile(
            "^(?=.)M*(C[MD]|D?C{0,3}" 
                    + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3}$");
    
    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```
이렇게 개선하면 isRomanNumeral이 빈번히 호출되는 상황에서 성능이 상당히 개선된다.

<br><br>

### ex) 오토박싱 안좋은 예
```Java
private static long sum() {
    Long sum = 0L;
    for(long i =0; i<=Integer.MAX_VALUE; i++) {
        sum += i;    
    }
    
    return sum;
}
```

위 코드의 경우, sum 변수 타입이 long이 아니라 Long이기 때문에 성능 오버헤드가 심하다 <br>
대략, long 타입인 i가 Long 타입인 sum에 더해질 때마다 불필요한 Long 인스턴스가 생성되기 때문이다. <br>
위 경우, Integer.MAX_VALUE 만큼(약 2^31) Long 인스턴스가 생성될 것이다. <br><br>

그러므로, 기본 타입을 되도록이면 사용하고 의도치 않은 오토박싱이 사용되었는지 잘 확인하자.
