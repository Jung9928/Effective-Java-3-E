# Item 49. 매개변수가 유효한지 검사하라

### 내용 요약 <br>
1. `매개변수 유효성 검사`의 중요성
    - 메소드나 생성자가 예상하지 못한 유효하지 않은 매개변수를 받으면, 프로그램이 예상치 못한 동작을 하거나 심지어 <br>
      충돌할 수 있다. <br><br>

    - 매개변수 검사를 통해 오류가 발생할 수 있는 지점을 명확히 할 수 있으며, 디버깅과 유지 보수를 쉽게 할 수 있다. <br><br>


2. 적절한 검사 위치
   - 공개된 메소드(public method)
     - 모든 공개된 메소드와 생성자는 전달받은 매개변수가 유효한 지 철저히 검사해야 한다. <br>
       이는 외부에서 메소드에 접근할 때의 신뢰성을 보장하기 위함이다. <br><br>
     
   - 비공개 메소드(private method)
     - 비공개 메소드는 일반적으로 호출하는 코드가 올바른 매개변수를 전달한다고 가정할 수 있지만 <br>
       여전히 복잡한 메소드의 경우 검사를 고려할 수 있다. <br><br>

       
3. 어떤 검사를 해야 하는가?
   - null 검사
     - 참조 타입 매개변수는 일반적으로 `null`이 허용되지 않으며 <br>
       `Objects.requireNonNull()` 메소드를 사용해 검사를 수행할 수 있다. <br><br>

   - 범위 검사
     - 숫자 타입의 매개변수는 특정 범위 내에 있어야 할 때가 많다. <br>
       예를 들어, 인덱스는 0 이상이어야 하며, 배열의 길이를 초과해서느 안된다. <br><br>

   - 상태 불변성 검사
     - 객체의 상태가 특정 조건을 만족해야 하는 경우 (ex : 정렬된 상태), 이 조건을 검사할 필요가 있다. <br><br>


4. 유효성 검사 실패 시의 처리
   - 검사 실패 시, 적절한 예외를 던져야 한다. <br>
     `IllegalArgumentException`, `IndexOutOfBoundsException`, `NullPointerException` 등이 상황에 맞게 사용될 수 있다. <br>

   - 예외 메세지는 구체적이어야 하며, 무엇이 잘못되었는지 명확히 알려줘야 한다. <br><br>


5. 성능과 유효성 검사
   - 대부분의 경우, 매개변수 검사는 성능에 큰 영향을 미치지 않는다. <br>
     오히려 오류를 조기에 발견해 프로그램의 안정성을 높이므로, 유효성 검사를 소홀히 해서는 안된다. <br><br>


### `기본적인 매개변수 유효성 검사` 
```java
import java.util.*;

public class Example {
    
    private String name;
    private int age;
   
    public Example(String name, int age) {
        // null 검사
        this.name = Objects.requireNonNull(name, "name must not be null");
        
        // 범위 검사
        if(age < 0 || age > 150) {
            throw new IllegalArgumentException("age must be between 0 and 150");
        }
        
        this.age = age;
    }
    
    public void setName(String name) {
        this.name = Objects.requireNonNull(name, "name must not be null");
    }
    
    public void setAge(int age) {
        if(age < 0 || age > 150) {
            throw new IllegalArgumentException("age must be between 0 and 150");
        }
        
        this.age = age;
    }
}
```
위 코드에서 `null 검사`와 `범위 검사`를 수행하여 어떤 매개변수가 유효하지 않은지와 <br>
유효하지 않은 매개변수가 전달되었을 때, 예외를 발생시켜 프로그램의 이상 동작을 막도록 하였다. <br>
