# Item 42. 익명 클래스보다는 람다를 사용하라

### 내용 요약 <br>
1. `익명 클래스`의 한계
    - 장황함
        - 익명 클래스는 코드가 길어지고 복잡해져, 코드의 가독성을 떨어뜨린다. <br><br>
      
    - 의미 전달의 어려움
         - 익명 클래스는 본질적으로 객체지향적이지만, 단일 메소드만 구현할 때는 함수적 개념이 더 잘 맞다. <br>
           이런 경우, 익명 클래스는 코드를 이해하기 어렵게 만들 수 있다. <br><br>

    - 예상치 못한 동작
      - 익명 클래스는 `this` 키워드가 익명 클래스 자체를 가리키며, 외부 클래스의 멤버에 접근할 때, 예상치 못한 동작을 할 수 있다. <br>


2. `람다 표현식`의 장점
   - 간결함 
     - 람다 표현식은 익명 클래스에 비해 훨씬 간결하고 명확하게 코드를 작성할 수 있다. <br><br>
     
   - 가독성
     - 람다 표현식은 코드의 의도를 명확히 드러내고 함수를 인라인으로 정의하는 것처럼 보이게 하여 가독성을 높인다. <br><br>
     
   - 타입 추론
     - 람다 표현식에서는 타입을 명시적으로 선언할 필요가 없으며, 컴파일러가 이를 추론한다. <br><br>
     
   - 함수형 프로그래밍에 적합
     - 람다 표현식은 함수형 인터페이스와 잘 어울리며, 함수형 프로그래밍을 장려한다. <br><br>

3. `람다 표현식`사용 시 주의사항
   - 복잡한 로직 
     - 람다 표현식은 간결함이 장점이지만, 지나치게 복잡한 로직을 람다로 작성하면 오히려 가독성이 떨어질 수 있다. <br>
       이럴 때는 메소드 참조 or 별도의 메소드를 정의하는 것이 더 나을 수 있다.
     
   - 디버깅 어려움
     - 익명 클래스보다 디버깅이 어려울 수 있으므로, 디버깅이 중요한 코드에서는 신중히 사용해야 한다. <br><br>


<br><br>


### `익명 클래스`를 사용한 경우
```java
import java.util.*;

public class StringSort {
    
   public static void main(String[] args) {
       String[] words = {"apple", "banana", "cherry"};
       
       // 익명 클래스를 사용한 Compartor 구현
       Comparator<String> comp = new Comparator<String>() {
           @Override
           public int compare(String s1, String s2) {
               return Integer.compare(s1.length(), s2.length());
           }
       };
       
      // 정렬 실행
      Arrays.sort(words, comp);
      System.out.println(Arrays.toString(words));
   }
}
```
위 코드에선 문자열 배열을 길이 기준으로 정렬하기 위해 `Comparator<String>` 인터페이스를 익명 클래스로 구현했다. <br>
이 방법은 코드가 장황해지고, 불필요한 보일러플레이트 코드가 추가된다. <br>


<br><br>


### `람다 표현식`을 사용한 경우
```java
import java.util.*;

public class StringSort {
    
   public static void main(String[] args) {
       String[] words = {"apple", "banana", "cherry"};
       
       // 람다 표현식을 사용한 Comparator 구현
      Comparator<String> comp = (s1, s2) -> Integer.compare(s1.length(), s2.length());
       
      // 정렬 실행
      Arrays.sort(words, comp);
      System.out.println(Arrays.toString(words));
   }
}
```
위 코드에서 동일한 기능을 람다 표현식을 사용하여 구현했다. <br>
코드가 훨씬 간결해졌고 함수형 인터페이스 `Comparator`의 구현이 명확하게 드러난다. <br>

