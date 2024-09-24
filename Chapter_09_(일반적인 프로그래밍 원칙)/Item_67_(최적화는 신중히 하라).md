# Item 67. 최적화는 신중히 하라

### 내용 요약 <br>
1. 성급한 최적화를 피하라
    - `성급한 최적화`는 코드의 명료성을 떨어뜨리고 버그 유발 가능성을 만든다. <br>
      성능 향상이 필요하지 않은 부분까지도 최적화하려는 시도는 오히려 코드의 가독성을 저하시킨다. <br><br>
   
    - 개발 초기 단계에서 성능을 고려하여 너무 많은 최적화를 시도하지 말고, `올바른 설계와 간결한 코드`에 집중하는 것이 더 중요하다. <br><br>

2. 성능 문제는 실제로 발생했을 때 해결하라 
    - 성능 문제가 실제로 발생하거나 테스트에서 드러났을 때만 최적화를 시작해야 한다. <br><br>

    - 프로그램이 제대로 동작하고 안정적인지 확인한 후, 성능 병목을 분석하고 구체적인 문제에 대해 최적화를 시도하는 것이 더 효과적이다. <br><br>


3. 성능을 측정하고 분석하라
    - 성능 문제를 해결하려면 먼저 문제의 `정확한 원인`을 분석해야 한다. <br>
      성능 저하의 원인을 모르는 상태에서 최적화를 시도하면 예상치 못한 결과가 나올 수 있다. <br><br>

    - 프로파일링 도구 등을 사용하여 실제로 병목이 되는 부분을 식별한 후, 그 부분에 집중하는 것이 좋다. <br><br>

4. 알고리즘과 자료구조의 선택이 중요하다
    - 가장 중요한 성능 최적화는 `올바른 알고리즘`과 `자료구조`를 선택하는 것이다. <br>
      이 단계에서의 최적화가 다른 어떤 성능 최적화보다 더 큰 효과를 발휘할 수 있다. <br><br>

5. 성능 개선 후에도 유지보수성을 고려하라
   - 성능을 개선한 코드도 가독성과 유지보수성을 잃지 않도록 주의해야 한다. <br>
     최적화를 너무 과도하게 하면, 코드가 복잡해지고 오류 발생 가능성이 높아질 수 있다. <br><br>



<br><br>



### 성급한 최적화의 문제
```java
public class OptimizationExample {

    private static final int SIZE = 1000000;
    private int[] values = new int[SIZE];
    
    public int sum() {
        int sum = 0;
        
        // 불필요한 최적화 : 루프 반복 수 줄이기 위해 여러 연산을 한 번에 묶음
        for(int i=0; i<SIZE; i += 2 ){
            sum += values[i] + values[i + 1];
        }
        return sum;
    }
}
```
위 코드에선 성능을 개선하려고 한 번의 루프에서 두 개의 값을 처리하도록 최적화했지만, 이는 가독성을 해치고 오히려 버그 발생 가능성을 높인다. <br>
실제로 성능 향상이 크게 발생하지 않는다면 이런 최적화는 불필요하다.

<br><br>



### 성능 측정과 프로파일링 후 최적화 예시
```java
import java.util.Arrays;

public class OptimizationExample {

    private static final int SIZE = 1000000;
    private int[] values = new int[SIZE];
    
    public int sum() {
        
        // 단순하고 명료한 코드
        return Arrays.stream(values).sum();
    }
}
```
위 코드에선 불필요하고 성급한 최적화 대신, 자바 스트림 API를 사용해 간결하고 명확하게 작성했다. <br>
실제 성능 문제가 발견되지 않는다면 이런 간결한 코드가 더 바람직하다.

<br><br>



### 성능 측정과 프로파일링 후 최적화 예시
```java
import java.util.List;
import java.util.ArrayList;

public class OptimizationExample {
    
    public List<Integer> filterEvenNumbers(List<Integer> numbers) {
        List<Integer> result = new ArrayList<>();
        
        // 성능 분석 후 병목 발견 : ArrayList의 재할당 비용이 문제일 수 있음
        result.ensureCapacity(numbers.size());
        
        for(int number : numbers) {
            if(number % 2 == 0) {
                result.add(number);
            }
        }
        return result;
    }
}
```
위 코드에선 `ArrayList`에 원소 추가 시, 자주 발생하는 `재할당 문제`를 해결하기 위해 `ensureCapacity`를 사용하여 성능을 개선했다. <br>
성능 병목을 프로파일링 도구로 분석한 후, 문제를 해결하는 방식으로 최적화가 이루어졌다. <br><br>

<br><br>

### 정리하자면
1. 최적화는 신중히할 것. 성능 문제를 미리 예단하지 말고 실제로 문제가 발생했을 때만 최적화 시도를 하자.
2. 프로파일링을 통해 성능 병목 구간을 명확히 확인한 후, 그에 맞는 `정확한 해결책`을 찾아야 한다.
3. 올바른 `알고리즘`과 `자료구조` 선택이 최적화의 핵심이다.
4. 최적화 후에도 코드의 `명료성`, `가독성`, `유지보수성`을 유지하는 것이 중요하다.

