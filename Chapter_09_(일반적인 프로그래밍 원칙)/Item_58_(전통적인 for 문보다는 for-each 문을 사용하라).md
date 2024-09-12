# Item 58. 전통적인 for문 보다는 for-each 문을 사용하라

### 내용 요약 <br>
1. `for-each`의 장점
    - 코드 간결성
      - 전통적인 for문에 비해 코드가 훨씬 간결하고 읽기 쉽다. 
      - 인덱스나 반복자(iterator)를 신경 쓸 필요 없이 간단히 컬렉션이나 배열의 요소에 접근할 수 있다. <br><br>

    - 오류 방지
      - for-each문은 인덱스 관리나 반복자 조작과 같은 복잡한 작업을 자동으로 처리하므로, 실수로 인한 오류(ex : 인덱스 범위 초과)를 방지할 수 있다. <br><br>

    - 유지보수성 향상
      - 코드가 간결하고 직관적이기 때문에, 시간이 지나도 쉽게 이해할 수 있다. <br><br>


2. 사용 가능한 상황
    - 컬렉션과 배열
      - for-each문은 모든 컬렉션(`List`, `Set`, `Map`, 등)과 배열에 사용할 수 있다. <br><br>
      
    - 가독성 중요한 반복문
      - 전통적인 for문보다 for-each문이 더 가독성 좋은 코드를 작성할 수 있다. <br><br>


3. 사용할 수 없는 상황
    - 컬렉션을 수정할 때
      - `for-each`문은 컬렉션을 수정하는 작업에 적합하지 않다. <br>
        예를 들어, 반복하면서 요소를 제거하거나 교체해야 하는 경우, 반복자를 사용하는 것이 좋다. <br><br>
      
    - 병렬 반복
      - 두 개 이상의 컬렉션을 동시에 순회해야 할 때는 인덱스를 사용하는 전통적인 for문이 필요할 수 있다. <br><br>


### 전통적인 `for`문을 사용하는 경우
```java
import java.util.*;

public class Item58Example {
    
    public static void main(String[] args) {
        List<String> items = new ArrayList<>();
        items.add("Apple");
        items.add("Banana");
        items.add("Orange");
        
        // 전통적인 for문을 사용한 반복
        for(int i=0; i<items.size(); i++) {
            System.out.println(items.get(i));
        }
    }
}
```
위 코드에서 전통적인 for문 방식은 인덱스를 수동으로 관리해야 하며, 코드가 상대적으로 복잡해 보일 수 있다. <br><br>


<br><br>



### `for-each`문을 사용하는 경우
```java
import java.util.*;

public class Item58Example {
    
    public static void main(String[] args) {
        List<String> items = new ArrayList<>();
        items.add("Apple");
        items.add("Banana");
        items.add("Orange");
        
        // for-each문을 사용한 반복
        for(String item : items) {
            System.out.println(item);
        }
    }
}
```
위 코드에서 `for-each`문은 인덱스를 수동으로 관리할 필요가 없으며 코드가 훨씬 간결하고 가독성이 좋다. <br><br>


### 배열에 `for-each`문을 사용하는 경우
```java
import java.util.*;

public class Item58Example {
    
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        
        // 배열에 대해 for-each문을 사용한 반복
        for(int number : numbers) {
            System.out.println(number);
        }
    }
}
```
위 코드에서 `for-each`문을 사용하여 인덱스를 관리할 필요가 없어서 코드를 깔끔하게 작성할 수 있다. <br><br>


### `for-each`문이 부적절한 상황
```java
import java.util.*;

public class Item58Example {
    
    public static void main(String[] args) {
        List<String> items = new ArrayList<>();
        items.add("Apple");
        items.add("Banana");
        items.add("Orange");
        
        // 요소를 제거하면서 반복해야 하는 경우
        Iterator<String> iterator = items.iterator();
        while(iterator.hasNext()) {
            String item = iterator.next();
            
            if("Banana".equals(item)) {
                iterator.remove();                  // for-each문에서는 불가능
            }
        }
        
        System.out.println(items);                  // [Apple, Orange]
    }
}
```
위 코드에서 `for-each`문은 내부적으로 반복자를 사용하지만, `iterator.remove()`같은 작업은 허용되지 않기 때문에 <br>
컬렉션의 요소를 제거할 때는 전통적인 `Iterator`를 사용해야 한다.









