# Item 28. 배열보다는 리스트를 사용하라

### 내용 요약 <br>
1. `배열`의 제한 사항
    - 크기 불변
      - 배열은 생성 시, 크기가 고정되며 이후에 크기 변경을 할 수 없다. <br><br>
      
    - 타입 안정성 부족
      - 배열은 타입 체크가 컴파일 타임에만 이루어지며, 타입 안정성을 보장하지 않는다. <br><br>

    - 유연성 부족
      - 배열은 다양한 기능 (ex : 동적 크기 조정, 요소 추가/삭제 등)을 제공하지 않는다. <br><br>


2. `리스트`의 장점
   - 동적 크기 조정
     - `List` 인터페이스를 구현한 컬렉션은 동적으로 크기를 조정할 수 있다. <br><br>
   
   - 풍부한 API
     - `List`는 요소의 추가, 삭제, 정렬, 검색 등 다양한 기능을 제공하는 메소드를 지원한다. <br><br>
     
   - 타입 안정성
     - `제네릭`을 사용하여 타입 안정성을 보장할 수 있다. <br><br>


### `배열`의 사용 예 (제한 사항)
```java
import java.util.Arrays;

public class ArrayExample {
   public static void main(String[] args) {
      String[] array = new String[10]; // 배열 생성
      array[0] = "Hello";
      array[1] = "World";

      // 배열의 크기를 변경할 수 없음
      // array = Arrays.copyOf(array, 20); // 크기 변경 불가

      for (int i = 0; i < array.length; i++) {
         System.out.println(array[i]); // null 값 출력 가능
      }
   }
}
```
`String[] array = new String[10];` 로 배열을 생성하였으며, 배열의 크기는 고정되어 있다. <br>
배열의 크기를 변경하거나 요소를 동적으로 추가할 수 없으며, 배열의 크기를 늘리려면 새로운 배열을 생성해야 한다. <br>
배열의 크기가 고정되기 때문에, 사용 중에 크기를 조정하거나 요소를 추가하는 것이 불편하다. <br><br>

<br>


### `리스트`의 사용 예 (권장)
```java
import java.util.ArrayList;
import java.util.List;

public class ListExample {
   public static void main(String[] args) {
      List<String> list = new ArrayList<>(); // 리스트 생성
      list.add("Hello");
      list.add("World");

      list.add("Java"); // 리스트는 동적으로 크기 조정 가능

      for (String str : list) {
         System.out.println(str); // null 값 출력 없음
      }
   }
}

```
`List<String> list = new ArrayList<>();` 로 `ArrayList`를 사용하여 리스트를 생성한다. 리스트는 크기가 동적으로 조정된다. <br>
`list.add("Java");`를 통해 요소를 동적으로 추가할 수 있다. <br>
`for (String str : list)` 루프를 사용하여 리스트의 요소를 간편하게 순회할 수 있다. 리스트는 요소를 안전하게 관리하며, `null` 값이 아닌 실제 값만 포함된다. <br><br>

### 정리하자면
- `List`는 배열보다 더 많은 기능과 유연성을 제공한다. 
- `배열`은 고정된 크기와 제한된 기능으로 인해 많은 경우에 비효율적일 수 있다.
- `List`는 동적 크기 조정, 다양한 API, 타입 안정성 등 많은 장점을 제공하므로, `배열` 대신 `List`를 사용하는 것이 나은 선택일 수 있다. 다만, 상황에 따라 맞는 방법을 쓰도록 하자
- `List`와 같은 컬렉션 프레임워크를 사용하면 코드를 보다 간결하고 안전하게 작성할 수 있다.