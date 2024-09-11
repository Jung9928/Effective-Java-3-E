# Item 57. 지역변수의 범위를 최소화하라

### 내용 요약 <br>
1. `지역변수`의 선언 위치
    - 변수는 최대한 사용하는 곳에서 가까운 위치에 선언해야 한다. <br>
      이렇게 하면 변수의 사용 범위가 명확해지고, 변수에 대한 의존성을 쉽게 추적할 수 있다. <br><br>
    
    - 루프 초기화
      - 루프 변수를 for문 안에서 선언하는 것이 바람직하다. <br>
        루프 외부에 변수를 선언하면 변수가 루프 외부에서도 사용될 수 있는 위험이 있으며, 범위가 불필요하게 확장된다.

<br><br>

2. 변수의 유효 범위
    - 가능한 블록`{}` 안에서 변수를 선언해 범위를 최소화한다. <br>
      블록은 지역변수를 제한적으로 사용할 수 있는 좋은 방법이며, 변수의 범위를 좁혀 코드의 안정성을 높인다. <br><br>


3. `코드 가독성`과 `유지보수성` 향상
    - 지역변수의 범위를 최소화하면 코드를 읽는 사람에게 변수의 수명과 유효 범위를 명확하게 보여준다. <br>
      또한, 코드 수정 시, 변수의 의도치 않은 재사용이나 오작동을 방지할 수 있다. <br><br>


4. 한 가지 일을 수행하는 변수를 사용하는 것이 좋다
    - 하나의 변수가 여러 목적을 위해 사용되면, 가독성이 떨어지고 혼란이 생길 수 있다. <br>
      변수를 하나의 용도로만 사용하도록 설계하면 코드의 의미가 분명해진다. <br><br>


5. 기타 권장 사항
   - 반복문을 사용할 때는 for-each를 활용
     - 전통적인 for문보다 for-each문을 사용하는 것이 변수를 잘못 사용할 위험을 줄이고 코드가 간결해진다. <br>
     
   - 람다 사용 시, 변수 범위에 유의
     - 람다 표현식에서 변수를 사용하면 변수 범위가 좁아지고 가독성이 좋아진다. <br><br>



<br><br>


### 잘못된 예시 : 변수의 범위가 넓은 경우
```java
import java.util.*;

public class Item57Example {
    
    public static void main(String[] args) {
        List<String> items = new ArrayList<>();
        items.add("Apple");
        items.add("Banana");
        items.add("Orange");
        
        // for문 외부에 변수를 선언 (권장되지 않음)
        int index;
        for(index = 0; index < items.size(); index++) {
            System.out.println(items.get(index));
        }
        
        // index가 for문 외부에서도 사용될 수 있음 (오류 발생 가능)
        System.out.println("최종 인덱스 : " + index);
    }
}
```
위 코드에서 `index`변수는 `for`문 외부에 선언되어, `for`문 외부에서도 사용될 수 있다. <br>
이로 인해, 코드가 복잡해질 수 있으며 `index`를 잘못 사용할 가능성이 높다. <br><br>





### 올바른 예시 : 변수의 범위가 넓은 경우
```java
import java.util.*;

public class Item57Example {
    
    public static void main(String[] args) {
        List<String> items = new ArrayList<>();
        items.add("Apple");
        items.add("Banana");
        items.add("Orange");
        
        // for문 외부에 변수를 선언 (권장되지 않음)
        for(int index = 0; index < items.size(); index++) {
            System.out.println(items.get(index));
        }
        
        // for-each를 사용하는 것이 더 좋은 방법
        for(String item : items) {
            System.out.println(item);    
        }
    }
}
```
위 코드에서 첫번째 `for`문에서 `index`변수를 선언해 해당 변수의 범위를 `for`블록 내로 제한했다. <br>
두 번째 `for-each`문은 더 간결하고 안전하게 리스트의 아이템을 순회할 수 있다. <br><br>





