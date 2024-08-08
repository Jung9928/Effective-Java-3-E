# Item 30. 이왕이면 제네릭타입으로 만들라

### 내용 요약 <br>
1. `제네릭 메소드`의 장점
    - 타입 안정성
        - 제네릭 메소드는 타입 파라미터를 사용하여 컴파일 타임에 타입 검사를 수행한다. 이를 통해 런타임 오류를 방지할 수 있다. <br><br>

    - 재사용성
        - 제네릭 메소드는 다양한 타입을 처리할 수 있으므로, 코드의 재사용성을 높이고 중복 코드를 줄일 수 있다. <br><br>

    - 타입 캐스팅 최소화
      - 제네릭 메소드를 사용하면 명시적인 타입 캐스팅을 피할 수 있으며, 이는 코드의 가독성과 안정성을 향상시킨다. <br><br>


2. `제네릭 메소드`의 정의
    - 제네릭 메소드는 메소드 선언에 타입 파라미터를 추가하여 정의한다. 일반적으로 메소드의 리턴 타입 앞에 타입 파라미터를 선언한다.
    - 예를 들어, `public <T> T getFirstElement(List<T> list)`와 같이 정의할 수 있다. <br><br>


### `제네릭 메소드` 예
```java
import java.util.*;

public class GenericMethodExample {
    
    // 제네릭 메소드 정의
   public static <T> T getFirstElement(List<T> list) {
       if(list.isEmpty()) {
           return null;
       }
       
       return list.get(0);
   }
    
    public static void main(String[] args) {
        List<String> stringList = List.of("Hello", "World");
        List<Integer> integerList = List.of(1, 2, 3);
        
        // 제네릭 메소드 호출
        String firstString = getFirstElement(stringList);
        Integer firstInteger = getFirstElement(integerList);
        
        System.out.println("First String : " + firstString);        // First String : Hello
        System.out.println("First Integer : " + firstInteger);      // First Integer : 1
    }
}
```
위 코드에서 `public static <T> T getFirstElement(List<T> list)`는 제네릭 메소드로, 타입 파라미터 `<T>`를 사용하여 리스트의 첫 번째 요소를 반환한다. <br>
`List<T>`는 메소드의 인자로 전달되는 리스트가 제네릭 타입 `T`를 가지도록 지정한다. <br>
메소드 본문에서 리스트가 비어 있지 않으면 첫 번째 요소를 반환하며, 비어 있을 경우 `null`을 반환한다. <br><br>

