# Item 27. 비검사 경고를 제거하라

### 내용 요약 <br>
1. `비검사 경고`의 원인
    - 비검사 경고는 `제네릭스`와 `원시` 타입 간의 상호 작용에서 발생한다. 
    - `제네릭` 타입을 사용한 코드가 원시 타입과 상호 작용할 때, 컴파일러는 타입 절보를 완벽히 검증할 수 없어서 `비검사 경고`를 발생시킨다.

<br>

2. `비검사 경고`를 제거하는 방법
   - `제네릭`타입을 일관되게 사용 : 가능한 한 모든 `제네릭` 타입에 대해 타입 파라미터를 명시적으로 선언한다.
   - `형변환`을 피하고 `제네릭 메소드`를 사용 : 비검사 형변환을 피하고, 타입 안정성을 보장하는 `제네릭 메소드`를 사용한다.
   - `@SuppressWarnings("unchecked")` 사용 : 비검사 경고를 무시할 수 있지만, 이는 최후의 수단으로 사용하며, 코드의 근본적인 문제를 해결하는 것이 좋다.

<br>


### 비검사 형변환의 예
```java
import java.util.ArrayList;
import java.util.List;

public class RawListExample {
    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        stringList.add("Hello");
        stringList.add("World");

        // 비검사 경고 발생
        @SuppressWarnings("unchecked")
        List<Object> objectList = (List<Object>) (List<?>) stringList;

        for (Object obj : objectList) {
            System.out.println(obj); // 타입 캐스팅으로 인한 위험 가능성
        }
    }
}
```
위 코드에서 `List<String> stringList`는 문자열만 저장할 수 있는 리스트입니다. <br>
`List<Object> objectList = (List<Object>) (List<?>) stringList;` 이 줄에서 비검사 형변환이 이루어진다. <br>
`원시`타입으로 변환하려고 할 때, 비검사 경고가 발생한다. <br>
`@SuppressWarnings("unchecked)` 어노테이션을 사용하여 컴파일러의 비검사 경고를 무시하지만, 이는 런타임에서 문제를 일으킬 가능성을 내포한다. <br>
`for (Object obj : objectList)` 루프에서 `Object` 타입으로 가져온 요소를 처리하는데, 이 경우 타입 불일치로 인한 런타임 오류가 발생할 수 있다. <br>

<br><br>

### 비검사 경고를 제거하는 올바른 방법 예
```java
import java.util.ArrayList;
import java.util.List;

public class SafeListExample {
    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        stringList.add("Hello");
        stringList.add("World");

        // 제네릭 타입을 사용하여 안전하게 처리
        List<String> safeList = new ArrayList<>(stringList);

        for (String str : safeList) {
            System.out.println(str); // 타입 안전성이 보장됨
        }
    }
}
```
위 코드에서 비검사 경고는 `제네릭스`와 `원시`타입 간의 상호작용에서 발생하며, 이를 해결하기 위해 `제네릭` 타입을 일관되게 사용하고 형변환을 피하는 것이 중요하다. <br>
`@SuppressWarnings("unchecked")`는 경고를 무시할 수 있지만, 최후의 수단으로 사용하고 가능한 한 타입 안정서을 보장하는 코드를 작성하는 것이 좋다. <br>

<br><br>



