# Item 14. Comparable을 구현할 지 고려하라

### 내용 요약 <br>
1. `Comparable` 인터페이스의 목적
    - 자연적인 순서를 정의하여 객체를 비교할 수 있게 한다.
    - 정렬된 컬렉션(`TreeSet`, `TreeMap`) 및 `Collections.sort`와 같은 메소드를 사용할 수 있게 한다.

<br>

2. Comparable 인터페이스의 메소드
   - `compareTo(T o)` 메소드를 구현해야 한다.
   - `compareTo` 메소드는 현재 객체가 주어진 객체보다 작으면 음수, 같으면 0, 크면 양수를 반환해야 한다.

<br>

3. Comparable 구현 시의 지침
   - 필드 비교는 주로 `Integer.compare`, `Double.compare` 같은 유틸리티 메소드를 사용한다.
   - 비교 대상 필드가 여러 개일 경우, 중요한 순서대로 비교한다.
   - `compareTo` 메소드가 `equals` 메소드와 일관되도록 한다.

<br>

4. 주의사항
   - `compareTo` 메소드를 구현할 때는 `NullPointerException`을 피하기 위해 null 허용을 하지 않는 게 좋다.
   - `compareTo` 메소드를 잘못 구현 시, `TreeSet`, `TreeMap` 과 같은 정렬된 컬렉션에서 오류가 발생할 수 있다.

<br>

### 기본 Comparable 구현 예시
```java
public class PhoneNumber implements Comparable<PhoneNumber> {
    private final int areaCode;
    private final int prefix;
    private final int lineNumber;
    
    public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
    }
    
    @Override
    public int compareTo(PhoneNumber pn) {
        int areaCodeDiff = Integer.compare(this.areaCode, pn.areaCode);
        
        if(areaCodeDiff != 0) {
            return areaCodeDiff;
        }
        
        int prefixDiff = Integer.compare(this.prefix, pn.prefix);
        if(prefixDiff != 0) {
            return prefixDiff;
        }
        
        return Integer.compare(this.lineNumber, pn.lineNumber);
    }
    
    @Override
    public String toString() {
        return String.format("(%03d) %03d-%04d", areaCode, prefix, lineNumber);
    }
    
    public static void main(String[] args) {
        List<PhoneNumber> phoneNumbers = new ArrayList<>();
        
        phoneNumbers.add(new PhoneNumber(123, 456, 7890));
        phoneNumbers.add(new PhoneNumber(123, 456, 1234));
        phoneNumbers.add(new PhoneNumber(123, 123, 7890));
        phoneNumbers.add(new PhoneNumber(111, 456, 7890));
        
        Collections.sort(phoneNumbers);
        
        for(PhoneNumber number : phoneNumbers) {
            System.out.println(number);
        }
    }
}
```

```text
### 실행결과 ###
(111) 456-7890
(123) 123-7890
(123) 456-1234
(123) 456-7890
```
위 코드에서 `PhoneNumber` 클래스가 `Comparable<PhoneNumber>` 인터페이스를 구현하여 `compareTo` 메소드를 정의한다. <br>
이 메소드는 `areaCode`를 비교하고, 같으면 `prefix`를 비교하고 또 같으면 `lineNumber`를 비교하여 객체의 순서를 정의한다. <br>

