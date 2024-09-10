# Item 43. 람다보다는 메소드 참조를 사용하라

### 내용 요약 <br>
1. `메소드 참조`의 장점
    - 간결함
        - 메소드 참조는 람다 표현식보다 짧고 간결하며, 중복된 코드나 불필요한 문법을 제거할 수 있다. <br><br>

    - 명확성
        - 메소드 참조를 사용하면 코드의 의도를 더욱 명확하게 전달할 수 있다. <br>
          메소드 참조는 의미를 명확히 드러내며, 코드의 가독성을 높인다. <br><br>

    - 중복 제거
        - 메소드 참조를 통해 이미 정의된 메소드를 재사용할 수 있어, 같은 기능을 여러번 구현하는 것을 피할 수 있다. <br>


<br><br>

2. `람다 표현식`과의 비교
   - 람다 표현식이 메소드 참조보다 명확한 경우도 있ㅇ으며, 메소드 참조가 더 복잡하거나 이해하기 어려운 경우, 람다 표현식을 사용하는 것이 바람직할 수 있다. <br><br>

   - 메소드 참조는 간결성과 가독성을 높이지만, 코드가 이해하기 어려워질 경우에는 람다를 사용하는 것이 더 나을 수 있다. <br><br>



### `람다 표현식`을 사용한 경우
```java
import java.util.*;

public class FrequencyCounter {
    
   public static void main(String[] args) {
       String[] words = {"apple", "banana", "cherry", "cherry", "banana", "apple"};
       
       Map<String, Integer> frequencyMap = new HashMap<>();
       
      // 람다 표현식을 사용한 빈도수 계산
      for(String word : words) {
          frequencyMap.merge(word, 1, (count, increment) -> count + increment);
      }
      
      System.out.println(frequencyMap);
   }
}
```
위 코드에서 `merge` 메소드를 사용해 단어의 빈도수를 계산할 때, 람다 표현식을 사용하여 값을 증가시켰다. <br>
이 방식도 충분히 직관적이지만, 코드가 길어지고 장황해 질 수 있다. <br><br>

<br><br>


### `메소드 참조`를 사용한 경우
```java
import java.util.*;

public class FrequencyCounter {
    
   public static void main(String[] args) {
       String[] words = {"apple", "banana", "cherry", "cherry", "banana", "apple"};
       
       Map<String, Integer> frequencyMap = new HashMap<>();
       
      // 람다 표현식을 사용한 빈도수 계산
      for(String word : words) {
          frequencyMap.merge(word, 1, Integer::sum);
      }
      
      System.out.println(frequencyMap);
   }
}
```
`Integer::sum` 메소드 참조를 사용했다. <br>
코드가 훨씬 간결해졌고, `Integer` 클래스의 `sum` 메소드가 두 인수를 더한다는 의미를 명확히 전달할 수 있다. <br>

<br><br>