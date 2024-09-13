# Item 59. 라이브러리를 익히고 사용하라

### 내용 요약 <br>
1. `자바 표준 라이브러리 사용`의 이점
    - 검증된 코드
        - 표준 라이브러리는 자바 커뮤니티에서 오랜 기간에 걸쳐 테스트되고 최적화된 코드이다. <br>
          직접 구현하는 것보다 안정적이고 성능 면에서 유리하다. <br><br>
      
    - 시간 절약
      - 자주 사용되는 기능을 이미 구현해 둔 라이브러리를 활용하면 코드 작성 시간을 크게 줄일 수 있다. <br><br>

    - 버그 위험 감소
        - 검증된 라이브러리를 사용하는 것은 수작업으로 코드를 작성하는 것보다 버그 발생 가능성을 줄여준다. <br><br>


2. 필요 시 라이브러리를 찾아 익히고 사용하라
    - 자바 개발자는 자주 사용되는 라이브러리나 유용한 API를 미리 숙지해두고, 필요할 때 적절히 활용하는 것이 중요하다. <br><br>
    - 표준 라이브러리 외에도 Apache Commons, Google Guava 등 널리 사용되는 오픈소스 라이브러리도 적극 활용할 수 있다. <br><br>


3. 재발명하지 말라
    - 표준 라이브러리나 외부 라이브러리가 제공하는 기능을 직접 구현하는 것은 불필요하고 비효율적이다. <br>
      이미 잘 구현된 기능을 사용하는 것이 더 좋다. <br><br>


4. 라이브러리 탐색 방법
   - 자바 표준 라이브러리는 Javadoc 문서를 통해 쉽게 탐색할 수 있다. <br><br>
   - 자주 사용되는 라이브러리의 패키지 구조와 주요 클래스들을 익혀 두면 필요할 때 빠르게 적용할 수 있다. <br><br>



### 잘못된 예시 : 라이브러리를 사용하지 않고 직접 구현하는 경우
```java
import java.util.*;

public class Item59Example {
    
    // 난수를 직접 생성하는 방식
    public static int randomNumber(int min, int max) {
        Random random = new Random();
        return min + random.nextInt(max - min + 1);
    }
    
    public static void main(String[] args) {
        System.out.println(randomNumber(1, 10));            // 1과 10 사이의 난수를 생성
    }
}
```
위 코드에서 난수를 생성하는 방법을 직접 구현했다. <br>
`Random` 클래스를 사용하고 있지만, 자바에서 제공하는 더 간편한 방식인 `ThreadLocalRandom`을 사용하지 않았다. <br>
이러한 방식은 불필요하게 직접 구현한 코드의 예로 볼 수 있다. 


<br><br>


### 올바른 예시 : 표준 라이브러리를 사용하는 경우
```java
import java.util.*;

public class Item59Example {
    
    public static void main(String[] args) {
        // ThreadLocalRandom을 사용하여 난수 생성
        int randomNum = ThreadLocalRandom.current().nextInt(1, 11);
        System.out.println(randomNum);                                  // 1과 10 사이의 난수를 생성
    }
}
```
위 코드에서 자바 표준 라이브러리에서 제공하는 `ThreadLocalRandom`클래스를 사용해 난수를 생성했다. <br>
이는 `Random`보다 더 효율적이고, 멀티스레드 환경에서도 안전하게 사용할 수 있다. <br>
라이브러리를 적절히 사용함으로써 코드가 간결하고 효율적으로 개선되었다. 


<br><br>



### 다른 라이브러리를 사용하는 경우
```java
import java.util.*;

public class Item59Example {
    
    public static void main(String[] args) {
        int[] numbers = {5, 3, 8, 1, 2};
        
        // Arrays.sort()를 사용하여 배열 정렬
        Arrays.sort(nummbers);
        
        // 정렬된 배열 출력
        System.out.println(Arrays.toString(numbers));       // [1, 2, 3, 5, 8]
    }
}
```
위 코드에서 자바의 `Arrays.sort()`메소드는 이미 잘 최적화된 정렬 알고리즘을 제공하며 <br>
직접 정렬 알고리즘을 구현할 필요 없이 간단히 배열을 정렬할 수 있다. <br><br>

<br><br>