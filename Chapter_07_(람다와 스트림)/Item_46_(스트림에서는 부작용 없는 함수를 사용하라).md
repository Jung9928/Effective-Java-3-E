# Item 46. 스트림에서는 부작용 없는 함수를 사용하라

### 내용 요약 <br>
1. 부작용 없는 함수의 중요성
    - 스트림의 중간연산 (`map`, `filter`, `sorted` 등)은 모두 지연 평가(lazy evaluation)되며, <br>
      최종 연산(`forEach`, `collect` 등)이 호출될 때 비로소 실행된다. <br>
      이 과정에서 부작용이 있는 함수를 사용하면 예측하지 못한 결과가 발생할 수 있다. <br><br>

    - 부작용 없는 함수는 입력을 받아 출력을 반환할 뿐, 외부 상태를 변경하지 않는다. <br>
      이러한 특성 덕분에 스트림은 안전하게 병렬 처리할 수 있다. <br><br>


2. 부작용이 있는 함수의 문제점
   - 부작용이 있는 함수는 스트림의 지연 평가와 결합될 때, 코드의 동작을 예측하기 어렵게 만든다. <br>
     예를 들어, 스트림 연산 중간에 외부 상태를 변경하면 코드의 실행 순서나 결과가 변경될 수 있다. <br><br>
   
   - 스트림의 병렬 처리에서 부작용이 있는 함수는 동기화 이슈를 야기할 수 있으며, 이는 성능저하나 예기치 못한 버그로 이어질 수 있다. <br>

<br>


3. 부작용이 없는 함수의 예
   - 중간 연산에서는 가능한 한 외부 상태를 변경하지 않는 순수 함수(pure function)를 사용해야 한다. <br>
     순수 함수는 동일한 입력에 대해 항상 동일한 출력을 반환하며, 외부에 영향을 미치지 않는다. <br><br>
   
   - 최종 연산에서도 부작용을 최소화하는 것이 좋지만, `forEach`같은 연산은 부작용을 허용한다. <br>
     이때도 부작용을 최소화하는 것이 중요하다. <br><br>

<br><br>

### `부작용이 있는` 함수를 사용한 경우
```java
import java.util.*;

public class SideEffectExample {
    
   public static void main(String[] args) {
       
        List<String> words = List.of("apple", "banana", "cherry");
        AtomicInteger counter = new AtomicInteger();
        
        // 부작용이 있는 함수 : 외부 상태를 변경 (counter 값을 증가)
        words.stream()
                .map(word -> {
                    counter.incrementAndGet();
                    return word.toUpperCase();
                })
                .forEach(System.out::println);
        
        // Counter 값 출력 (3)
        System.out.println("Counter : " + counter.get());
   }
}
```
위 코드에서 `map`연산 안에서 `counter.incrementAndGet()`을 호출하여 외부 상태(여기서는 `counter`변수)를 변경하고 있다. <br>
이런 방식은 스트림의 지연 평가와 결합될 때 코드의 동작을 예측하지 어렵게 만들 수 있다. <br>
특히, 병렬 스트림을 사용할 경우, 동시성 문제가 발생할 수 있다. <br><br>

<br><br>


### `부작용이 없는` 함수를 사용한 경우
```java
import java.util.*;

public class NoSideEffectExample {
    
   public static void main(String[] args) {
       
        List<String> words = List.of("apple", "banana", "cherry");
        
        // 부작용이 있는 함수 : 외부 상태를 변경하지 않음
        words.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
        
   }
}
```
위 코드에서 스트림을 사용해 각 단어를 대문자로 변환하지만, 외부 상태를 변경하지 않는다. <br>
이런 순수 함수는 스트림의 중간 연산으로 사용할 때 안전하며 코드가 예측 가능하고 병렬처리에 적합하다. <br><br>

<br><br>
