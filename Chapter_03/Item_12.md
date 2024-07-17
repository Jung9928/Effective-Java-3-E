# Item 12. toString을 항상 재정의하라.

### 내용 요약 <br>
1. toString 재정의의 중요성
    - `toString` 메소드를 재정의하지 않으면, 클래스 이름과 해시 코드를 반환한다. -> 쓸모가 없다
    - 좋은 `toString` 메소드는 객체에 대한 정보를 제공하고 디버깅, 로깅에 유용하다.
    - 객체의 주요 정보를 포함하여 사용자가 이해할 수 있는 형식으로 반환하는 것이 좋다.

<br>

2. toString 재정의의 일반적인 지침
   - 객체가 포함하는 모든 중요한 정보를 반환한다.
   - 반환 형식은 사람이 읽을 수 있도록 명확하고 간결해야 한다.
   - 가능한 한 그 객체를 다시 생성하는데 필요한 모든 정보를 포함시킨다.

<br>

3. 문서화
   - `toString`의 출력 형식을 명확히 문서화하여, 이를 사용하는 사람이 예상할 수 있게 한다.
   - 출력 형식을 문서화하지 않으면 출력 형식을 변경할 때 문제가 발생할 수 있다.

<br>


### 기본 toString 메소드를 재정의하지 않은 클래스 예시
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

    @Override
    public int hashCode() {
        int result = Integer.hashCode(areaCode);
        result = 31 * result + Integer.hashCode(prefix);
        result = 31 * result + Integer.hashCode(lineNumber);
        return result;
      }
      
      
    // toString 메서드를 재정의하지 않음
}
```
위 코드에서는 `toString` 메소드를 재정의하지 않았기 때문에 기본 구현이 사용된다. <br>
기본 구현은 클래스 이름과 해시코드를 반환하므로 유용하지 않다. <br><br>

### `toString` 메소드를 올바르게 재정의한 예시
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

    @Override
    public int hashCode() {
        int result = Integer.hashCode(areaCode);
        result = 31 * result + Integer.hashCode(prefix);
        result = 31 * result + Integer.hashCode(lineNumber);
        return result;
      }
      
      
    @Override
    public String toString() {
        return String.format("(%03d) %03d-%04d", areaCode, prefix, lineNumber);        
    }
    
    public static void main(String[] args) {
        PhoneNumber phoneNumber = new PhoneNumber(123, 456, 7890);
        System.out.println(number);             // 출력 : (123) 456-7890
    }
}
```
위 코드에서는 `toString` 메소드를 재정의하여 전화번호의 각 부분을 적절한 형식으로 반환한다. <br>
이를 통해, 객체에 대한 유용한 정보를 제공할 수 있다.

<br><br>

### 정리하자면
- `toString` 메서드를 재정의하여 객체의 유용한 정보를 반환하도록 해야 합니다.
- 반환 형식은 명확하고 간결해야 하며, 사람이 읽을 수 있는 형식이어야 합니다.
- 출력 형식을 문서화하여 사용자에게 예상 가능한 형식을 제공합니다.
- 재정의된 `toString` 메서드는 디버깅, 로깅 등에서 객체의 상태를 명확히 표현하는 데 도움이 됩니다.