# Item 50. 적시에 방어적 복사본을 만들라

### 내용 요약 <br>
1. `방어적 복사`의 필요성
   - 외부 영향 차단
       - 외부에서 제공된 가변 객체(ex : 배열, 컬렉션 등)를 그대로 사용하면 <br> 
         외부에서 이 객체를 변경할 수 있어 내부 상태가 의도치 않게 변할 수 있다. <br>
         이러한 상태 변경은 버그나 보안 취약점을 초래할 수 있다. <br>

   - 불변성 보장
     - 클래스가 불변성을 제공하려면, 외부에서 전달된 객체를 직접 사용하지 않고 방어적 복사본을 만들어 사용해야 한다.  <br><br>
       이를 통해, 내부 상태를 안전하게 보호할 수 있다. <br>

<br>

2. `방어적 복사`적용 방법
    - 생성자
        - 가변 객체를 인자로 받는 생성자에서는, 인자로 받은 객체의 방어적 복사본을 만들어 <br>
          내부 필드에 저장해야 한다. <br>
          이렇게 하면 원본 객체의 변경이 내부 상태에 영향을 미치지 않는다. <br><br>

    - 메소드
        - 메소드에서 가변 객체를 반환할 때도 방어적 복사본을 반환해야 한다. <br>
          그래야 반환된 객체가 외부에서 수정되어도 내부 상태가 변경되지 않는다. <br>
    
<br>

3. `방어적 복사`의 비용
   - 성능 비용
     - 방어적 복사는 성능 측면에서 비용이 발생할 수 있다. <br>
       특히, 큰 객체나 복잡한 데이터 구조의 경우 복사에 드는 시간이 문제가 될 수 있다. <br>
       하지만 안전성 확보를 위한 필수적인 비용으로 여겨야 한다.
     
   - 방어적 복사의 예외
     - 방어적 복사를 사용하지 않는 경우도 있다. <br>
       예를 들어, 성능이 매우 중요한 상황에서 외부와의 철저한 협약이 이루어진 경우, <br>
       방어적 복사를 생략할 수 있다. 하지만 이런 경우는 매우 드물고, <br>
       일반적으로는 방어적 복사를 적용하는 것이 안전하다. <br><br>


4. `방어적 복사`의 예외
   - 방어적 복사를 사용하지 않는 경우도 있다. 예를 들어, 성능이 매우 중요한 상황에서 <br>
     외부와의 철저한 협약이 이루어진 경우, 방어적 복사를 생략할 수 있다. <br>
     하지만 이런 경우는 매우 드물고, 일반적으로는 방어적 복사를 적용하는 것이 안전하다. <br><br>


### `방어적 복사`를 사용하지 않은 경우
```java
import java.util.*;

public class Period {
    
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        if(start.compareTo(end) > 0) {
            throw new IllegalArgumentException("시작 날짜는 종료 날짜보다 앞서야 한다.");
        }
        
        this.start = start;
        this.end = end;
    }
    
    public Date getStart() {
        return start;
    }
    
    public Date getEnd() {
        return end;
    }
}
```
위 코드에서 `Period`객체가 생성된 후 외부에서 `start`와 `end`를 변경할 수 있다. <br>
예를 들어, `Date`객체를 직접 반환하면 외부 코드에서 반환된 `Date` 객체를 수정하여 <br> 
`Period` 객체의 불변성을 깨뜨릴 수 있다.

<br><br>

### `방어적 복사`를 적용한 경우
```java
import java.util.*;

public class Period {
    
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());             // 방어적 복사
        this.end = new Date(end.getTime());                 // 방어적 복사
        
        if(this.start.compareTo(this.end) > 0) {
            throw new IllegalArgumentException("시작 날짜는 종료 날짜보다 앞서야 한다.");
        }
    }
    
    public Date getStart() {
        return new Date(start.getTime());                   // 방어적 복사
    }
    
    public Date getEnd() {
        return new Date(end.getTime());                     // 방어적 복사
    }
}
```
위 코드에서 `getStart()`와 `getEnd()`메소드도 내부 필드의 방어적 복사본을 반환한다. <br>
이렇게 하면 반환된 `Date`객체가 외부에서 수정되더라도, `Period`객체의 상태에는 영향을 미치지 않는다. <br>

