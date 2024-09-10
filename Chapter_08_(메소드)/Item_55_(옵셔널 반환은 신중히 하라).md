# Item 55. 옵셔널 반환은 신중히 하라

### 내용 요약 <br>
1. `Optional`의 장점
    - 명시적으로 값이 없을 수 있음을 표현한다. <br>
      메소드 시그니처에서 `Optional<T>`를 반환하면, 호출자는 반환값이 없을 수도 있음을 즉시 알 수 있다. <br><br>

    - `null`을 직접 반환하는 대신 `Optional`을 사용하면, 호출하는 쪽에서 `null`을 처리해야 하는 부담이 줄어들고, `NullPointerException`을 예방할 수 있다. <br><br>


2. `Optional` 사용 시 주의점
   - 성능 문제
     - `Optional` 객체는 추가적인 래핑을 포함하기 때문에 성능이 중요한 메소드나 저수준의 API에서 반환 타입으로 사용하기엔 부적절할 수 있다. <br>
       특히, 컬렉션이나 배열 같은 상황에서 `Optional`을 반환하면, 추가적인 오버헤드가 발생할 수 있다. <br><br>

   - 대체 불가능한 경우
     - `Optional`은 참조 타입에 적합하며, 원시 타입엔 사용할 수 없다. <br>
       예를 들어, `OptionalInt`, `OptionalDouble`과 같은 타입도 있지만, 성능 면에서 불리할 수 있으므로 주의해야 한다. <br><br>

3. `Optional` 반환에 적합한 상황
   - 메소드의 반환 값이 필수적이지 않고, 값이 없음을 명시적으로 나타낼 때 적합하다. <br><br>
   - 값을 찾이 못했을 때 `null`을 반환하는 대신 `Optional.empty()`을 반환하며, 호출하는 측에서 `Optional.isPresent()`을 통해 <br>
     값을 확인하도록 강제할 수 있다. <br><br>

4. `Optional`을 남용하지 말 것
   - 모든 메소드에서 `Optional`을 반환하는 것이 바람직하지 않다. <br>
     특히, 성능이 중요한 메소드나 빈번히 호출되는 메소드에서는 `Optional` 대신 다른 방법을 사용할 수 도 있다. <br><br>
     
   - 컬렉션에서는 `빈 컬렉션`을 반환하는 것이 더 좋은 선택이다. <br>
     예를 들어, `List`나 `Set`은 값이 없을 때, `Optional`이 아니라 빈 List나 빈 Set을 반환하는 것이 적절하다. <br><br>


<br><br>


### `Optional`을 사용하는 예시
```java
import java.util.*;

public class Item55Example {
    
    // 값을 찾지 못할 수 있는 메소드 : Optional로 감싸서 반환
    public static Optional<String> findName(String[] names, String target) {
        for(String name : names) {
            if(name.equals(target)) {
                return Optional.of(name);
            }
        }
        
        return Optional.empty();
    }
   
    
    public static void main(String[] args) {
        String[] names = {"Alice", "Bob", "Charlie"};
        
        // Optional로 반환된 값을 안전하게 처리
        Optional<String> foundName = findName(names, "Bob");
        foundName.ifPresent(name -> System.out.println("찾은 이름 : " + name));     // 출력 : 찾은 이름 : Bob
        
        // 값이 없을 경우 기본 값 사용
        String defaultName = findName(names, "Daniel").orElse("Unknown");
        System.out.println("찾은 이름 : "  + defaultName);                          // 출력 : 찾은 이름 : Unknown 
    }
}
```
위 코드에서 `findName`메소드는 이름을 찾지 못했을 때, `Optional.empty()`를 반환한다. <br>
이 메소드를 사용하는 코드는 `Optional.ifPresent()` 또는 `Optional.orElse()` 등을 사용해 `null`을 처리하는 부담 없이 안전하게 값을 다룬다. <br><br>


<br><br>


### `Optional`을 남용한 잘못된 예시
```java
import java.util.*;

public class Item55Example {
    
    // 잘못된 예 : 컬렉션을 Optional로 감싸는 경우
    public static Optional<List<String>> getNames(boolean returnEmpty) {
        List<String> names = List.of("Alice", "Bob", "Charlie");
        return returnEmpty ? Optional.empty() : Optional.of(names);
    }
   
    
    public static void main(String[] args) {
        String[] names = {"Alice", "Bob", "Charlie"};
        
        // Optional로 감싼 컬렉션을 반환한는 것은 부적절
        Optional<String> foundName = getNames(true);
        names.ifPresent(list -> list.forEach(System.out::println));
    }
}
```
위 코드에서 `Optional`로 감싸는 것은 부적절하다. <br>
값이 없는 상황에서는 `Optional.empty()` 대신 빈 리스트를 반환하는 것이 더 좋다. <br>
`Optional`은 값을 가질 수 없음을 명시적으로 나타내는 용도로만 사용하는 것이 적절하다. <br><br>


<br><br>



### `컬렉션의 경우 빈 컬렉션을 반환`하는 것이 더 적절한 예시
```java
import java.util.*;

public class Item55Example {
    
    // 빈 컬렉션을 반환하는 예
    public static Optional<List<String>> getNames(boolean returnEmpty) {
        List<String> names = List.of("Alice", "Bob", "Charlie");
        return returnEmpty ? Collections.emptyList() : names;
    }
   
    
    public static void main(String[] args) {
        // 빈 리스트를 반환하는 것은 Optional보다 적절함
        List<String> names = getNames(true);
        names.forEach(System.out::println);
    }
}
```
위 코드는 `Optional`을 사용하지 않고 빈 리스트를 반환하는 것이 더 적절한 경우이다. <br>
이 방식은 호출자가 `null` 체크를 할 필요 없이 반환된 리스트가 비어 있는지 여부만 확인하면 된다. <br><br>



### 정리하자면
1. `Optional`은 메소드가 값이 없을 수도 있음을 명확히 표현할 수 있는 좋은 방법이지만, 성능이나 사용 패턴을 고려해 신중하게 사용해야 한다.
2. `참조 타입`에서 `Optional`을 사용하는 것이 적합하며, 원시 타입이나 빈 컬렉션을 반환할 때는 `빈 객체`를 반환하는 것이 더 나은 설계입니다. 
3. 빈 컬렉션, 빈 배열 등은 `Optional`로 감쌀 필요 없이 반환하면 된다.


      

