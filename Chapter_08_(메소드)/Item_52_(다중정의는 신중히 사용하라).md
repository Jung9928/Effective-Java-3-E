# Item 52. 다중정의는 신중히 사용하라

### 내용 요약 <br>
1. `다중정의`와 `재정의`의 차이
    - 다중정의 (Overloading)
      - 같은 이름의 메소드가 서로 다른 매개변수 리스트를 가질 때 발생한다. <br>
        메소드 호출 시, 컴파일러는 전달된 매개변수의 타입과 개수를 기준으로 적합한 메소드를 선택한다. <br><br>

    - 재정의 (Overriding)
      - 상위 클래스나 인터페이스에서 정의된 메소드를 하위 클래스에서 동일한 시그니처로 재정의하는 것을 의미한다. <br>
        재정의는 런타임에 결정된다. <br><br>


2. `다중정의`의 문제점
    - 다중정의는 종종 혼란을 초래한다. 특히, 메소드가 상속된 클래스에서 재정의될 때, 다중정의된 메소드와의 충돌로 인해 예상치 못한 결과가 발생할 수 있다.
    - 메소드 호출 시, 컴파일러는 매갭젼수 타입을 기준으로 메소드를 선택하는데 이는 런타임에 결정되는 것이 아니라 컴파일 시점에 결정되기 때문에 <br>
      의도와 다른 메소드가 호출될 수 있다. <br><br>


3. `가변인수`와 `다중정의`
    - 가변인수 메소드와 다중정의된 메소드를 함께 사용할 경우, 어떤 메소드가 호출될지 예측하기 어려울 수 있다. <br>
      따라서, 가변인수 메소드와 다중정의 메소드를 함께 사용할 때는 더욱 신중해야 한다. <br><br>

      
4. `다중정의` 대신 `메소드 이름`을 명확히 하라
    - 다중정의를 남용하는 대신, 메소드의 이름을 명확히 구분하는 것이 좋다. <br>
      예를 들어, `read` 대신 `readBytes`와 `readLines`처럼 구체적인 이름을 사용하는 것이 바람직하다. <br><br>


5. `런타임 타입`과 `컴파일타임 타입`의 불일치 주의
    - 다형성과 다중정의가 함께 사용될 때, 호출되는 메소드가 컴파일 시점과 런타임 시점에 서로 다를 수 있다. <br>
      다중정의는 컴파일 시점에 결정되지만 재정의는 런타임에 결정되므로, 개발자가 예상한 것과 다른 메소드가 호출될 수 있다. <br><br>


<br><br>

### `다중정의`의 문제 예시
```java
import java.util.*;

public class CollectionClassifier {
    
    public static String classify(Set<?> s) {
        return "집합";
    }

   public static String classify(List<?> lst) {
      return "리스트";
   }

   public static String classify(Collection<?> c) {
      return "그 외";
   }
   
   public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<String>(),
                new HashMap<String, String>().values()
        };
        
        for(Collection<?> c : collections) {
            System.out.println(classify(c));                // 기대 결과는 각각 "집합", "리스트", "그 외"
        }
   }
}
```
위 코드에서 `classify` 메소드는 `Set`, `List`, `Collection`을 각각 처리하도록 다중정의되었다. <br>
하지만 실제 출력 결과는 모두 "그 외"이다. 이는 `for`루프에서 `classify(c)`가 호출될 때, `c`의 타입은 <br>
런타임에 결정되지만 메소드 다중정의는 컴파일 시점에 결정되기 때문이다. <br><br>

따라서, `classify(Collection<?> c)`가 항상 호출된다. <br><br> 



### `해결 방법` : 명확한 메소드 이름 사용
```java
import java.util.*;

public class CollectionClassifier {
    
    public static String classify(Set<?> s) {
        return "집합";
    }

   public static String classify(List<?> lst) {
      return "리스트";
   }

   public static String classify(Collection<?> c) {
      return "그 외";
   }
   
   public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<String>(),
                new HashMap<String, String>().values()
        };
        
        for(Collection<?> c : collections) {
            if(c instanceof Set) {
                System.out.println(classifySet((Set<?>) c));
            }
            else if(c instanceof List) {
                System.out.println(classifyList((List<?>) c));
            }
            else {
                System.out.println(classifyCollection(c));
            }
        }
   }
}
```
위 코드에서 `Set`, `List`, `Collection`을 처리하는 각각의 메소드에 명확한 이름을 부여하여 혼란을 피한다. <br>
또한, `instanceof`를 사용하여 올바른 메소드를 호출하도록 한다. <br><br>

