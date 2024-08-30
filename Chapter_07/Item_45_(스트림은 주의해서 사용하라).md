# Item 45. 스트림은 주의해서 사용하라

### 내용 요약 <br>
1. `스트림`의 장점
    - 간결함과 가독성
      - 스트림은 데이터 처리 파이프라인을 간결하고 선언적으로 작성할 수 있게 해준다. <br>
        여러 연산을 메소드 체이닝으로 연결해서 깔끔하게 표현할 수 있다. <br><br>

    - 병렬 처리
      - 스트림 API는 손쉽게 병렬 처리를 지원하며, `parallelStream()`을 사용해 데이터 처리 작업을 병렬로 수행할 수 있다. <br><br>

    - 함수형 프로그래밍 스타일
      - 스트림은 함수형 프로그래밍 스타일을 장려하며, 불변성과 사이드 이펙트가 없는 코드를 작성하는데 도움을 준다. <br><br>


2. `스트림`사용 시 주의사항
    - 읽기 어려운 코드
      - 스트림을 과도하게 사용하면 오히려 코드가 복잡해지고, 읽기 어려워질 수 있다. <br>
        특히, 중첩된 스트림 연산이나 복잡한 로직이 포함된 경우, 코드를 이해하는 데 시간이 걸릴 수 있다. <br><br>
      
    - 디버깅 어려움
      - 스트림 연산은 중간 연산이 지연 평가(lazy evaluation)되기 때문에, 디버깅 시 문제의 원인을 추적하기 어려울 수 있다. <br><br>

    - 전통적인 루프와 비교
      - 스트림을 사용하는 것이 항상 좋은 것은 아니다. <br>
        간단한 반복문이나 순차적인 처리가 필요한 경우, 기존의 `for`루프가 더 명확하고 이해하기 쉬운 선택일 수 있다. <br><br>

    - 스트림의 적합성
      - 모든 문제에 스트림이 적합한 것은 아니다. <br>
        예를 들어, 스트림을 사용하여 상태를 변형하거나 외부 변수를 수정하는 것은 권장되지 않는다. <br><br>


3. `스트림`과 `컬렉션`
   - 스트림과 컬렉션은 서로 보완적인 관계다. 스트림은 데이터를 처리하고 변환하는데 유용하고, 컬렉션은 데이터를 저장하고 관리하는 데 유용하다. <br>
     데이터를 처리할 때는 스트림을, 데이터 구조를 유지하고 조작할 때는 컬렉션을 사용하는 것이 좋다. <br><br>


<br><br>


### `스트림`을 사용한 경우
```java
import java.util.*;

public class StreamExample {
    
   public static void main(String[] args) {
       
        List<String> words = List.of("apple", "banana", "cherry", "date");
        
        // 스트림을 사용하여 단어를 대문자로 변환하고, 길이가 5보다 큰 단어만 필터링
        List<String> result = words.stream()
                .map(String::toUpperCase)
                .filter(word -> word.length() > 5)
                .collect(Collectors.toList());
        
        System.out.println(result);         // [BANANA, CHERRY]
   }
}
```
위 코드에서 스트림을 사용해서 리스트의 각 요소를 변환하고 필터링한 후, 결과를 새로운 리스트로 수집한다. <br>
스트림을 사용하면 코드가 간결하고 선언적이며, 한눈에 데이터 변환 과정을 이해할 수 있다. <br><br>

<br><br>


### `스트림`대신 전통적인 반복문을 사용한 경우
```java
import java.util.*;

public class StreamExample {
    
   public static void main(String[] args) {
       
        List<String> words = List.of("apple", "banana", "cherry", "date");
        List<String> result = new ArrayList<>();
        
        // 전통적인 for 루프를 사용하여 단어를 대문자로 변환하고, 길이가 5보다 큰 단어만 필터링
        for(String word : words) {
            String upperCaseWord = word.toUpperCase();
            
            if(upperCaseWord.length() > 5) {
                result.add(upperCaseWord);
            }
        }
        
        System.out.println(result);         // [BANANA, CHERRY]
   }
}
```
위 코드에선 `for`루프를 사용했다. `스트림`보다 코드가 장황해질 수 있지만 더 직관적이고 이해하기 쉬울 수 있다. 

<br><br>

