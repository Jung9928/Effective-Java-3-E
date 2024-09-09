# Item 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라

### 내용 요약 <br>
1. `null`반환의 문제점
    - 메소드가 `null`을 반환하면, 호출하는 쪽에서는 항상 `null`체크를 해야 한다. <br>
      이를 잊어버리거나 실수하면 `NullPointerException`이 발생할 수 있다. <br><br>
   
    - `null`체크가 많아지면 코드가 복잡해지고 가독성이 떨어진다. <br>
      특히, 다중 컬렉션을 다루는 코드에서 이 문제는 더 심각해진다. <br><br>
      


2. `빈 컬렉션/배열`을 반환하는 장점
    - 빈 컬렉션이나 배열을 반환하면 `null`체크가 필요 없어지므로 코드가 더 간결하고 안정적으로 변한다. <br><br>

    - 자바에서는 빈 컬렉션을 효율적으로 반환할 수 있도록 `Collections.emptyList()`, `Collections.emptySet()`, `Collections.emptyMap()` <br>
      같은 메소드를 제공하낟. <br>
      이러한 메소드는 메모리와 성능 측면에서 매우 효율적이다. <br><br>

    - 배열의 경우에도 `new Type[0]` 처럼 빈 배열을 반환하는 것이 일반적이다. <br><br>


3. `빈 객체 반환의 성능 우려는 거의 없다`
    - 빈 컬렉션이나 배열을 반환하는 것은 매우 가볍고 효율적인 작업이므로 성능 문제를 일으킬 가능성은 거의 없다. <br>
      자바 표준 라이브러리는 빈 컬렉션을 재사용하며, 새로운 메모리를 할당하지 않는다. <br><br>


4. `코드의 일관성`과 `안정성 향상` 
   - 항상 빈 컬렉션이나 배열을 반환하는 메소드는 일관성과 예측 가능성을 높인다. <br>
     호출하는 쪽에서 `null` 체크를 하지 않아도 되므로 코드의 유지보수가 쉬워지고, 버그가 발생할 가능성이 줄어든다. <br><br>



### `null을 반환하는 경우`의 잘못된 예시
```java
import java.util.*;

public class Item54Example {
    
    private final List<String> items;
    
    public Item54Example(List<String> items) {
        this.items = items;
    }
    
    // items가 비어있으면 null을 반환하는 메소드
   public List<String> getItems() {
        return items.isEmpty() ? null : items;
   }
   
   public static void main(String[] args) {
        Item54Example example = new Item54Example(List.of());
        
        // null 체크를 잊으면 NullPointerException 발생 가능
      if(example.getItems() != null) {
          for(String item : example.getItems()) {
              System.out.println(item);
          }
      }
   }
   
}
```
위 코드에서 `getItems()` 메소드가 빈 리스트일 때 `null`을 반환한다. <br>
이를 호출하는 코드는 항상 `null`체크를 해야 하며, 이를 잊으면 `NullPointerException`이 발생할 수 있다. <br><br>




### `빈 컬렉션을 반환하는 경우` -> 올바른 예시
```java
import java.util.*;

public class Item54Example {
    
    private final List<String> items;
    
    public Item54Example(List<String> items) {
        this.items = items;
    }
    
    // null 대신 빈 리스트를 반환하는 메소드
   public List<String> getItems() {
        return items.isEmpty() ? Collections.emptyList() : items;
   }
   
   public static void main(String[] args) {
        Item54Example example = new Item54Example(List.of());
        
        // null 체크가 필요없고 안전하게 사용할 수 있음
       for(String item : example.getItems()) {
           System.out.println(item);
       }
   }
}
```
위 코드에서 `getItems()` 메소드가 빈 리스트일 때 `Collections.emptyList()`을 반환한다. <br>
이로인해 호출하는 쪽에서는 `null`체크를 할 필요 없이 안전하게 코드를 작성할 수 있다. <br>
또한, `Collections.emptyList()`는 메모리 효율적인 방식으로 빈 리스트를 반환하므로 성능에도 문제가 없다. <br><br>







