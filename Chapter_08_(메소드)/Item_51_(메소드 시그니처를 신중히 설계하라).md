# Item 51. 메소드 시그니처를 신중히 설계하라

### 내용 요약 <br>
1. `메소드 이름을 신중히 선택하라`
    - 메소드 이름은 그 메소드가 수행하는 작업을 명확하게 설명해야 한다. <br>
      직관적인 이름을 사용하면 코드의 가독성이 높아진다. <br><br>
   
    - 같은 클래스 내에서 일관된 이름 규칙을 따르도록 하여, 유사한 동작을 하는 메소드들이 비슷한 이름을 갖게 한다. <br><br>


2. `매개변수 목록을 짧게 유지하라`
   - 매개변수의 수가 많을수록 메소드 사용이 복잡해진다. 매개변수는 가능하면 4개 이하로 제한하는 것이 좋다. <br><br>
   - 여러 개의 관련 매개변수는 하나의 객체로 묶어 전달하거나, 빌더 패턴을 사용해 복잡한 매개변수를 처리하는 방법도 있다. <br><br>


3. `매개변수 타입으로 클래스 대신 인터페이스를 사용하라`
   - 매개변수로 구체적인 클래스보다는 인터페이스를 사용하여 메소드의 유연성을 높인다. <br>
     예를 들어, `List` 대신 `Collection`을 사용하는 것이 더 유연하다. <br><br>


4. `boolean`대신 `enum`을 고려하라
   - 메소드에 boolean 값을 전달하는 경우, true/false 만으로는 그 의미를 이해하기 어려울 수 있다. <br>
     대신 의미를 명확히 전달할 수 있는 `enum` 타입을 사용하는 것이 좋다. <br><br>


5. `오버로딩`을 신중히 사용하라
   - 오버로딩은 유사한 이름의 메소드들을 제공하는 방법이지만, 혼동을 일으킬 수 있다. 
   - 오버로딩된 메소드들이 호출될 때 혼란이 생기지 않도록 주의해야 하며, 이름을 명확하게 구분하는 것도 좋은 방법이다. <br><br>


6. 장황한 매개변수 리스트 대신 `빌더 패턴`을 고려하라
   - 매개변수 목록이 길어질 경우, 특히 선택적인 매개변수가 많다면, 빌더 패턴을 사용해 더 읽기 쉽고 사용하기 쉬운 메소드들을 제공할 수 있다. <br><br>


<br><br>


### `매개변수 타입`으로 `인터페이스`를 사용하는 예
```java
import java.util.*;

public class Item51Example {
    
    // 구체적인 클래스 대신 인터페이스를 사용
   public static int calculateTotalLength(Collection<String> strings) {
       int totalLength = 0;
       
       for(String str : strings) {
           totalLength += str.length();
       }
       
       return totalLength;
   }
}
```
위 코드에서 `Collection` 인터페이스를 매개변수로 받아, `List`, `Set` 등 다양한 타입의 컬렉션을 사용할 수 있게 한다. <br>
구체적인 클래스 대신 인터페이스를 사용함으로써 메소드의 유연성을 높이고, 다양한 상황에 적용할 수 있게 한다. <br><br>



### `boolean` 대신 `enum`을 사용하는 예
```java
import java.util.*;

public class Item51Example {
    
    // 파일 열기 모드를 나타내는 enum 타입
   public enum FileMode {
       READ, WRITE, APPEND
    }
    
    public void openFile(String fileName, FileMode mode) {
       switch (mode) {
          case READ:
              // 읽기 모드로 파일 열기
             break;
             
          case WRTIE:
              // 쓰기 모드로 파일 열기
             break;
             
          case APPEND:
              // 추가 모드로 파일 열기
             break;
       }
    }
}
```
위 코드에서 파일을 열 때 `boolean`을 사용하는 대신 `FileMode`라는 `enum`을 사용해, <br>
파일을 여는 모드를 명확하게 나타낸다. 이를 통해 코드의 가독성이 높아지고, 실수를 줄일 수 있다. <br><br>




