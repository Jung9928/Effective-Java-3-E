# Item 47. 반환 타입으로는 스트림보다 컬렉션이 낫다

### 내용 요약 <br>
1. `스트림`과 `컬렉션`의 차이점
    - 스트림의 중간연산 (`map`, `filter`, `sorted` 등)은 모두 지연 평가(lazy evaluation)되며, <br>
      최종 연산(`forEach`, `collect` 등)이 호출될 때 비로소 실행된다. <br>
      이 과정에서 부작용이 있는 함수를 사용하면 예측하지 못한 결과가 발생할 수 있다. <br><br>

    - 부작용 없는 함수는 입력을 받아 출력을 반환할 뿐, 외부 상태를 변경하지 않는다. <br>
      이러한 특성 덕분에 스트림은 안전하게 병렬 처리할 수 있다. <br><br>


2. `컬렉션 반환`의 장점
    - 부작용이 있는 함수는 스트림의 지연 평가와 결합될 때, 코드의 동작을 예측하기 어렵게 만든다. <br>
      예를 들어, 스트림 연산 중간에 외부 상태를 변경하면 코드의 실행 순서나 결과가 변경될 수 있다. <br><br>

    - 스트림의 병렬 처리에서 부작용이 있는 함수는 동기화 이슈를 야기할 수 있으며, 이는 성능저하나 예기치 못한 버그로 이어질 수 있다. <br>

<br>


3. `스트림`대신 `컬렉션`을 반환해야 하는 이유
    - 다양한 처리 작업
      - 반환된 데이터를 여러 번 반복하거나 다양한 방식으로 처리해야 할 경우, 컬렉션이 더 적합하다. <br>

    - 호환성
      - 컬렉션을 반환하면 사용자가 원하는대로 스트림으로 변환하여 사용할 수 있으므로 더 유연하다. <br>


<br><br>

4. `스트림`을 반환해도 좋은 경우
   - 연속적인 데이터 처리 <br>
     - 데이터를 한 번만 처리하고, 그 이후에는 재사용할 필요가 없는 경우, 스트림을 반환할 수 있다. <br>

   - 데이터 생성의 효율성 <br>
     - 데이터를 즉시 생성하지 않고, 필요할 때 생성하는 것이 중요한 경우, 스트림이 적합할 수 있다. <br>

<br><br>


### `컬렉션`을 반환하는 메소드
```java
import java.util.*;

public class CollectionExample {
    
    public static List<String> getWords() {
        List<String> words = new ArrayList<>();
        words.add("apple");
        words.add("banana");
        words.add("cherry");
        return words;
    }
    
   public static void main(String[] args) {
    
     List<String> words = getWords();
     words.forEach(System.out::println);        // 여러 번 순회 가능
      
     System.out.println(words.size());          // 크기 확인 가능
   }
}
```
위 코드에서 List를 스트림으로 변환하여 필요에 따라 스트림 연산도 사용할 수 있다. <br>

<br><br>

### `스트림`을 반환하는 메소드
```java
import java.util.*;

public class StreamExample {
    
    public static Stream<String> getWordsStream() {
        return Stream.of("apple", "banana", "cherry");
    }
    
   public static void main(String[] args) {
        Stream<String> wordsStream = getWordsStream();
        wordsStream.forEach(System.out::println);               // 한 번만 순회 가능

      // wordsStream.forEach(System.out::println); // 예외 발생: 스트림은 재사용 불가
   }
}
```
위 코드에서 스트림은 한 번만 사용할 수 있으며, 한 번 소비된 후에는 다시 사용할 수 없다. <br>
만약, 데이터를 여러 번 사용해야 한다면 스트림을 반환하는 것은 적절치 않다. <br>
<br><br>