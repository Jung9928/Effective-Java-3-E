# Item 48. 스트림 병렬화는 주의해서 적용하라

### 내용 요약 <br>
1. `벙렬 스트림`의 이점과 위험
    - 이점
      - 병렬 스트림을 사용하면 멀티 코어 프로세서를 활용하여 작업을 병렬로 처리할 수 있으므로, 처리 시간이 단축될 수 있다. <br>
        특히, 대규모 데이터 집합을 처리할 때 유리하다. <br><br>
      
    - 위험
      - 병렬 처리는 항상 성능을 개선하지 않는다. <br>
        데이터 크기가 작거나 작업이 단순할 때는 병렬화로 인한 오버헤드가 더 커질 수 있다. <br>
        또한, 병렬 처리에서 데이터 접근이나 변경이 안전하게 이루어지지 않으면 동시성 문제로 인해 예기치 않은 결과가 발생할 수 있다. <br><br>


2. `병렬 스트림`을 사용할 때 고려할 사항
    - 작업 비용
      - 병렬 처리에서 각 스레드가 수행하는 작업의 비용이 충분히 커야 병렬화의 이점이 있다. <br>
        작업이 가벼우면 병렬 처리 오버헤드로 인한 성능 저하가 발생할 수 있다. <br><br>

    - 데이터 크기
      - 처리할 데이터의 크기가 클수록 병렬 스트림의 효과가 크다. <br>
        작은 데이터 세트는 병렬 처리의 장점을 살리기 어렵다. <br><br>

    - 데이터 구조
      - 데이터 소스의 구조에 따라 병렬 처리의 성능이 달라질 수 있다. <br>
        예를 들어, `ArrayList`는 인덱스 기반으로 접근이 가능해 병렬 처리에 유리하지만 <br>
        `LinkedList`는 병렬처리에 적합하지 않다. <br><br>

    - 상태 공유 피하기
      - 병렬 스트림에서 상태를 공유하면 동기화 문제로 인해 성능 저하 or 버그가 발생할 수 있다. <br>
        따라서, 스트림 내부에서 상태를 공유하지 않도록 해야 한다. <br><br>

    - 성능 테스트
      - 병렬 스트림을 적용하기 전에 반드시 성능 테스트를 통해 병렬화의 효과를 검증해야 한다. <br>
        기대했던 성능 향상이 이루어지지 않을 수 있기 때문이다. <br><br>


3. `병렬화`의 올바른 적용
    - `메소드 참조`와 `람다`를 사용해 병렬화된 작업을 간결하게 작성할 수 있다. <br>
    - `적절한 데이터 구조`를 선택하고 스트림 연산에서 부작용이 없는 함수만 사용해야 한다. <br>
    - 병렬 스트림을 사용할 땐, `parallel()` 메소드를 호출하여 병렬 모드를 활성화한다. <br>


<br><br>

### `병렬 스트림` 사용 예시
```java
import java.util.*;

public class ParallelStreamExample {
    
   public static void main(String[] args) {
    
     List<String> words = Arrays.asList("apple", "banana", "cherry", "date", "elderberry");
     
     // 병렬 스트림 사용
       words.parallelStream()
               .map(String::toUpperCase)
               .forEach(System.out::println);
   }
}
```
위 코드에서 `parallelStream()`을 사용해 단어 목록을 병렬로 처리했다. <br>
병렬 처리가 이루어지므로 출력 순서는 보장되지 않는다. <br><br>




### `병렬 스트림`의 성능 테스트 예시
```java
import java.util.*;

public class ParallelStreamPerformanceTest {
    
   public static void main(String[] args) {
    
     long n =10_000_000L;
     
     // 순차 스트림 사용
       long start = System.currentTimeMillis();
       long sumSequential = LongStream.rangeClosed(1, n)
               .sum();
       
       long durationSequential = System.currentTimeMillis() - start;
       
       System.out.println("순차 스트림 합계 : " + sumSequential + ", 시간 : " +durationSequential + " ms");
       
       // 병렬 스트림 사용
       start = System.currentTimeMillis();
       long sumParallel = LongStream.rangeClosed(1, n)
               .parallel()
               .sum();
       
       long durationParallel = System.currentTimeMillis() - start;
       
       System.out.println("병렬 스트림 합계 : " + sumParallel + ", 시간 : " + durationParallel + " ms");
   }
}
```
위 코드에서 병렬 스트림이 순차 스트림보다 더 빠를 수 있음을 보여준다. <br>
하지만, 병렬화가 항상 순차 처리보다 빠른 것은 아니며 작업의 특성에 따라 달라질 수 있다. <br><br>



