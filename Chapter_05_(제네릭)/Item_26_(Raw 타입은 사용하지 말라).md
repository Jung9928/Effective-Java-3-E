# Item 26. Raw(원시) 타입은 사용하지 말라

### 내용 요약 <br>
1. `원시`타입의 문제점
    - 타입 안정성 부족 : 원시 타입은 제네릭 타입의 타입 정보를 잃어버리기 때문에 컴파일러가 타입 안정성을 보장하지 않는다.
    - 런타임 오류 가능성 : 원시 타입을 사용할 경우, 타입 불일치로 인해 런타임 오류가 발생할 수 있다.
    - 경고 및 비효율성 : 컴파일러는 원시 타입을 사용할 때, 타입 안정성에 대한 경고를 표시하며, 이는 잠재적인 오류를 암시한다.
   
<br>


2. `제네릭`타입의 이점
   - 타입 안정성 보장 : `제네릭`타입을 사용하면 컴파일 타임에 타입을 체크하여 타입 안정성을 보장한다.
   - 코드의 명확성 및 유지보수성 : 제네릭 타입을 사용하면 타입이 명확하게 정의되어 코드가 더 읽기 쉽고 유지보수하기 쉬워진다.


<br>

1. `원시`타입 사용을 피하는 방법
   - `제네릭`타입을 사용하여 타입을 명시적으로 선언 : `원시`타입 대신 `제네릭`타입을 사용하여 타입 안정성을 확보한다.
   - 형변환을 피하고, `제네릭`타입의 이점을 활용 : `제네릭`타입의 안전한 캐스팅을 통해 런타임 오류를 방지한다.


<br>


### `원시`타입을 사용하는 예시 (권장하지 않음)
```java
import java.util.*;

public class RawTypeExample {
    public static void main(String[] args) {
        List rawList = new ArrayList();         // 원시 타입 사용
        rawList.add("Hello");
        rawList.add(123);                       // 타입 안정성 결여
       
        for(Object obj : rawList) {
            String str = (String) obj;          // 타입 캐스팅 필요
            System.out.println(str);            // 런타임에서 ClassCastException 발생 가능
        }
    }
}
```
`rawList`는 제네릭 타입의 `원시`타입으로 선언되어있으며, 다양한 타입의 객체를 저장할 수 있다.<br>
`for`루프에서 `Object`타입으로 리스트의 각 요소를 가져온 후, 명시적으로 `String`으로 캐스팅해야 한다. <br>
이로 인해 잘못된 타입 캐스팅으로 인해 런타임 오류가 발생할 수 있다. <br>

### `제네릭`타입을 사용하는 예시 (권장)
```java
import java.util.*;

public class GenericTypeExample {
    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();        // 제네릭 타입 사용
        stringList.add("Hello");
        
        // stringList.add(123);                             // 컴파일 오류 발생 -> 타입 안정성 보장
       
       for(String str : stringList) {
           System.out.println(str);                         // 타입 캐스팅 필요 없음
       }
    }
}
```
`List<String>`을 사용하여 문자열만 저장할 수 있는 리스트를 선언한다. <br>
제네릭 타입을 사용하면 컴파일 타임에 타입 안정성이 보장되며, 타입 캐스팅이 필요 없으므로 런타임 오류의 가능성이 줄어든다 <br>

