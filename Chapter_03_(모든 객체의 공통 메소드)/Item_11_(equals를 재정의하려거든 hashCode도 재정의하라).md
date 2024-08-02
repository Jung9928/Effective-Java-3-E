# Item 11. equals를 재정의하려거든 hashCode도 재정의하라.

### 내용 요약 <br>
1. equals와 hashCode의 관계
   - `equals` 메소드가 두 객체를 같다고 판단하면, 이 두 객체의 `hashCode`값은 반드시 같아야 한다. <br>
     그렇지 않으면 해시 기반 컬렉션(`HashMap`, `HashSet` 등)에서 제대로 동작하지 않는다.
<br><br>

2. hashCode 재정의의 중요성
   - `equals` 메소드를 재정의한 후, `hashCode`를 재정의하지 않으면 같은 객체라도 다른 해시코드 값을 가질 수 있다.<br>
     이는 해시 기반 컬렉션에서 객체를 검색하거나 저장할 때, 에러를 발생시킨다.
<br><br>

3. hashCode 재정의의 규약
   - 같은 객체에 대해 여러 번 호출해도 항상 같은 정수 값을 반환해야 한다. <br>
     (단, 어플리케이션이 실행되는 동안 객체의 상태가 변하지 않는 경우에만 해당한다.) <br><br>
   - 두 객체가 같다면(`equals(Object)` 메소드가 `true`를 반환), 반드시 같은 해시코드를 반환해야 한다. <br><br>
   - 두 객체가 다르더라도 반드시 다른 해시 코드를 반환할 필욘 없지만 다른 객체에 대해 다른 해시 코드를 반환하는 것이 좋다. <br><br>

### 잘못된 hashCode 재정의 예시
```java
public class PhoneNumber {
    private final int areaCode;
    private final int prefix;
    private final int lineNumber;
    
    public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
    }
    
    @Override
    public boolean equals(Object o) {
        if (o == this) 
            return true;
        
        if(!(o instanceof PhoneNumber)) 
            return false;
        
        PhoneNumber pn = (PhoneNumber) o;
        
        return pn.areaCode == areaCode && pn.prefix == prefix && pn.lineNumber == lineNumber;
    }
    
    // hashCode 메소드를 재정의하지 않음
}
```
위 예시에서는 `equals` 메소드만 재정의했지만, `hashCode` 메소드를 재정의하진 않았다. 이 경우, 해시 기반 컬렉션에서 예기치 못한 에러가 발생할 수 있다.

<br><br>

### 올바른 hashCode 재정의 예시
```java
import java.util.*;

public class PhoneNumber {
    private final int areaCode;
    private final int prefix;
    private final int lineNumber;

    public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this)
            return true;

        if(!(o instanceof PhoneNumber))
            return false;

        PhoneNumber pn = (PhoneNumber) o;

        return pn.areaCode == areaCode && pn.prefix == prefix && pn.lineNumber == lineNumber;
    }
    
    @Override
    public int hashCode() {
        int result = Integer.hashCode(areaCode);
        result = 31 * result + Integer.hashCode(prefix);
        result = 31 * result + Integer.hashCode(lineNumber);
        return result;
    }
    
    public static void main(String[] args) {
        PhoneNumber p1 = new PhoneNumber(123, 456, 7890);
        PhoneNumber p2 = new PhoneNumber(123, 456, 7890);
        
        // equals 테스트
        System.out.println(p1.equals(p2));                       // true

        // hashCode 테스트
        System.out.println(p1.hashCode() == p2.hashCode());      // true

        // 해시 기반 컬렉션 테스트
        HashSet<PhoneNumber> set = new java.util.HashSet<>();
        set.add(p1);
        System.out.println(p1.equals(p2));                       // true
    }
}
```
위 예시에서는 `hashCode` 메소드를 올바르게 재정의하여 `equals`메소드와 일관성을 유지한다. <br>
이를 통해 해시 기반 컬렉션에서 제대로 동작한다. <br><br>

### 정리하자면
- 해시 기반 컬렉션에서 예기치 못한 오류 발생을 방지하려면 `hashCode` 메소드를 재정의할 때, `equals` 메소드와 일관성을 유지하는 것이 중요하다. 