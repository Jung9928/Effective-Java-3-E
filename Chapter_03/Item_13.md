# Item 13. clone 재정의는 주의해서 진행하라.

### 내용 요약 <br>
1. `clone` 메소드의 기본 동작
    - `Object` 클래스의 `clone` 메소드는 객체의 얕은 복사를 수행한다.
    - 클래스가 `Cloneable` 인터페이스를 구현하지 않으면 `CloneNotSupportedException`이 발생한다.


2. `clone` 메소드 재정의의 기본 원칙
   - `Cloneable` 인터페이스를 구현하고 `clone` 메소드를 재정의해야 한다.
   - `super.clone()`을 호출하여 부모 클래스의 `clone` 메소드를 호출한다.
   - 깊은 복사가 필요한 경우, 각 필드에 대해 직접 복사를 구현해야 한다.


3. 깊은 복사가 필요한 경우
   - 배열이나 가변 객체를 포함하는 클래스의 경우, 깊은 복사가 필요할 수 있다.
   - 배열이나 가변 객체를 복사하여 독립적인 복사본을 생성하자.


4. 주의사항
   - `clone` 메소드는 생성자와는 다르게 작동하므로 초기화 블록을 거치지 않는다.
   - 불변 객체를 만드는 것이 가능하다면, `clone` 메소드를 사용하지 않는 것이 좋다.
   - 복잡한 객체의 경우, `clone` 메소드를 재정의하는 것보다 복사 생성자나 복사 팩토리 메소드를 사용하는 것이 바람직 하다.


### 기본 `clone` 재정의 예시
```java
public class PhoneNumber implements Cloneable {
    private final int areaCode;
    private final int prefix;
    private final int lineNumber;
    
    public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
    }
    
    @Override
    public PhoneNumber clone() {
        
        try {
            return (PhoneNumber) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();             // 발생하지 않음
        }
    }
    
    @Override
    public String toString() {
        return String.format("(%03d) %03d-%04d", areaCode, prefix, lineNumber);
    }
    
    public static void main(String[] args) {
       PhoneNumber original = new PhoneNumber(123, 456, 7890);
       PhoneNumber copy = original.clone();

       System.out.println(original);  // 출력: (123) 456-7890
       System.out.println(copy);      // 출력: (123) 456-7890
    }
}
```
위 예시에서 `PhoneNumber` 클래스가 `Cloneable` 인터페이스를 구현하고, `clone` 메소드를 재정의하여 객체의 얕은 복사를 수행한다. <br>
`super.clone()` 을 호출하여 부모 클래스의 `clone` 메소드를 호출하고, `CloneNotSupportedException`을 처리한다. <br><br>

### 깊은 복사가 필요한 경우의 예시
```java
import java.util.Arrays;

public class Stack implements Cloneable {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;                          // 인스턴스를 null 처리
        return result;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

    @Override
    public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone();         // 깊은 복사
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();                 // 발생하지 않음
        }
    }

    public static void main(String[] args) {
        Stack stack = new Stack();
        stack.push("item1");
        stack.push("item2");
        
        Stack copy = stack.clone();
        
        System.out.println(stack.pop());                // 출력: item2
        System.out.println(copy.pop());                 // 출력: item2
    }
}
```
위 예시에서는 `Stack` 클래스가 `Cloneable` 인터페이스를 구현하고, `clone`메소드를 재정의하여 깊은 복사를 수행한다. <br>
`elements` 배열을 복사하여 독립적인 복사본을 생성한다. <br><br>


### 정리하자면
- `clone` 메소드를 재정의할 땐, `Cloneable` 인터페이스를 구현하고 `super.clone()`을 호출하자.
- 얕은 복사로 충분한 경우, 기본 재정의를 사용하고 깊은 복사가 필요한 경우 각 필드를 직접 복사하자.
- `clone` 메소드 재정의는 주의해서 진행해야 하며, 오히려 불변 객체나 복사생성자를 사용하는 것이 더 나은 경우도 있다.