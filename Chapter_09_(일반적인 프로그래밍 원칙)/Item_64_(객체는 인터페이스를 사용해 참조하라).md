# Item 64. 객체는 인터페이스를 사용해 참조하라

### 내용 요약 <br>
1. 인터페이스를 통해 참조
    - `String`객체는 `불변(immutable)`이므로, 문자열을 연결할 때마다 새로운 `String`객체가 생성된다. <br>
      이 과정에서 이전 문자열의 값을 복사하고, 새로운 객체를 생성하는데 많은 비용이 든다. <br><br>

2. 구현체가 고유한 기능이 있다면 클래스 사용
    - 특히, 루프 안에서 문자열 연결을 `+`연산자로 하면 성능이 크게 저하될 수 있다. <br>
      각 반복에서 새로운 `String`객체가 생성되기 때문에 반복횟수가 늘어나면 성능이 기하급수적으로 떨어진다. <br><br>

3. 유연성 증가
    - `StringBuilder`는 `가변(mutable)`객체로, 문자열을 더할 때마다 새로운 객체를 생성하지 않고, 내부 버퍼에 문자열을 추가한다. <br>
      따라서, 성능이 매우 효율적이다.

4. 구현 클래스는 생성 시에만 사용
    - `StringBuffer`는 `Thread-safe`한 버전이다. 다중 스레드 환경에서는 `StringBuffer`를 사용하고 <br>
      단일 스레드 환경에서는 성능이 더 빠른 `StringBuilder`를 사용해야 한다. <br><br>


<br><br>




### 잘못된 예시 : 클래스 타입으로 참조
```java
import java.util.*;

public class Item64Example {

    public static void main(String[] args) {
        
        // 구체적인 클래스인 ArrayList로 참조
        ArrayList<String> list = new ArrayList<>();
        list.add("Effective Java");
        list.add("Item 64");
        
        for(String item : list) {
            System.out.println(item);
        }
    }
}
```
위 코드에선 `ArrayList`라는 구체적인 클래스 타입으로 객체를 참조하고 있다. <br>
이 경우, 나중에 `LinkedList`나 다른 리스트 구현체로 변경하려면 코드를 수정해야 하고, 유지보수가 어려워진다. <br>
`ArrayList`의 특정 기능을 사용하지 않는다면 인터페이스를 사용하는 것이 더 좋다. <br><br>

<br><br>

### 올바른 예시 : 인터페이스로 참조
```java
import java.util.*;

public class Item64Example {

    public static void main(String[] args) {
        
        // 구체적인 클래스인 ArrayList로 참조
        ArrayList<String> list = new ArrayList<>();
        list.add("Effective Java");
        list.add("Item 64");
        
        for(String item : list) {
            System.out.println(item);
        }
    }
}
```
위 코드에선 `List`인터페이스로 객체를 참조하고 있다. <br>
나중에 `ArrayList`대신 `LinkedList` 같은 다른 구현체로 쉽게 변경할 수 있다. <br>
또한, 코드의 유연성과 재사용성이 높아진다. <br><br>


### 다른 구현체로 쉽게 변경할 수 있는 예시
```java
import java.util.*;

public class Item64Example {

    public static void main(String[] args) {
        
        // LinkedList로 구현체 변경
        List<String> list = new LinkedList<>();             // 구현체를 LinkedList로 변경
        list.add("Effective Java");
        list.add("Item 64");
        
        for(String item : list) {
            System.out.println(item);
        }
    }
}
```
위 코드에선 `List`인터페이스로 참조하면서, 구현체는 `LinkedList`로 변경한 예시이다. <br>
코드의 다른 부분을 수정할 필요 없이 구현체를 자유롭게 교체할 수 있어 유지보수가 용이하다.

<br><br>

### 정리하자면
1. 객체 참조 시, `구체적인 클래스보다 인터페이스를 사용하는 것이 좋다` -> 코드가 더 유연해지고 유지보수와 확장이 쉬워진다.
2. `구체적인 구현체는 객체 생성 시에만 사용`하고, 이후에는 인터페이스로 참조하는 방식을 권장한다.
3. 특별한 이유가 없다면 언제나 인터페이스를 사용하여 `구현체 변경의 유연성`을 확보하는 것이 더 좋다.

