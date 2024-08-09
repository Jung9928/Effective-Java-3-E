# Item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

### 내용 요약 <br>
1. `와일드카드`의 필요성
    - `제네릭`타입을 사용할 때, 특정 타입만을 처리할 수 있는 경우보다 더 많은 타입에 대해 유연하게 처리할 필요가 있다.
    - `와일드카드`는 제네릭 타입에 대해 보다 포괄적인 처리를 가능하게 해주며, 이를 통해 API 유연성을 높일 수 있다. <br><br>

2. `한정적` 와일드카드의 사용
   - `<? extends T>`
     - 상한 결계를 가진 와일드카드. 
     - 타입이 `T`를 상속하거나 구현한 모든 타입에 대해 사용할 수 있으며 readonly 자료구조에 적합하다. <br><br>
     
   - `<? super T>` : 하한 경계를 가진 와일드카드.
     - 하한 경계를 가진 와일드카드.
     - 타입이 `T`의 슈퍼클래스인 모든 타입에 대해 사용할 수 있다. -> 쓰기 전용 자료구조에 적합하다. <br><br>

3. `한정적` 와일드카드 사용의 장점
   - API 유연성 증가 : 다양한 타입에 대해 제네릭 메소드나 클래스를 유연하게 처리 가능
   - 타입 안정성 유지 : 와일드카드를 사용하여 타입 안정성을 유지하면서도 다양한 타입을 처리할 수 있다. <br><br>


### `한정적 와일드카드` 예
```java
import java.util.*;

public class WildcardExample {
    
    // 제네릭 메소드 : 상한 경계를 가진 와일드카드 사용
    public static <T> void printElements(List<? extends T> list, Class<T> clazz) {
        for(T element : list) {
            System.out.println(clazz.cast(element));        // 타입 안정성을 보장하며 출력
        }
    }
    
    
    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        stringList.add("Hello");
        stringList.add("World");
        
        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        integerList.add(2);
        
        // 상한 경계를 가진 와일드카드를 사용하여 출력
        printElements(stringList, String.class);        // Hello \n world
        printElements(integerList, Integer.class);      // 1\n2
    }
}
```
위 코드에서 상한 경계를 가진 와일드카드(<? extends T>)는 읽기 전용 자료구조에 적합하며 <br> 
하한 경계를 가진 와일드카드(<? super T>)는 쓰기 전용 자료구조에 적합합니다. <br><br>
이러한 기법을 사용하면 코드의 재사용성을 높이고, 타입 안전성을 유지하면서도 다양한 타입을 처리할 수 있는 유연한 API를 만들 수 있다.