# Item 88. readObject 메소드는 방어적으로 작성하라

### 내용 요약 <br>
1. 역직렬화 과정의 위험성
   - 직렬화된 객체가 `외부 소스`에서 전송될 경우, 신뢰할 수 없는 데이터를 역직렬화할 가능성이 있다. <br><br>

   - 악의적인 데이터나 손상된 데이터가 역직렬화될 수 있어 `보안 취약점`이 발생할 수 있다. <br><br>

2. `readObject()`메소드를 방어적으로 작성해야 하는 이유
   - 직렬화된 데이터가 의도한 객체 상태를 훼손할 수 있기 때문에, 객체의 무결성을 보장하기 위해 `검증`과 `방어적 복사`가 필요하다. <br><br>

   - 특히, 불변 객체나 민감한 데이터를 다루는 객체는 역직렬화 시 예기치 않은 변경으로 인해 `보안 문제`가 발생할 수 있다. <br><br>

3. 방어적 복사
   - 객체 내부에서 `직렬화된 가변 객체`가 있을 경우, 그 객체의 참조를 그대로 사용하지 말고 <br>
     복사본을 만들어 사용해야 한다. 이는 외부에서 가변 객체를 통해 원래 객체의 상태를 변경하는 것을 방지한다. <br><br>

4. 유효성 검사
   - 역직렬화된 데이터가 유효한 지 확인하는 절차를 반드시 수행해야 한다. <br>
     예를 들어, `불변식(invariant)`이나 특정 제약 조건을 확인하여 데이터가 올바르게 직렬화되었는지 검증해야 한다. <br><br>

   - 잘못된 데이터가 역직렬화되면 `예외를 던져`문제를 조기에 감지하고 처리를 중단해야 한다. <br><br>

5. `readObject`메소드와 상속
   - `readObject` 메소드를 구현할 때는 `부모 클래스의 readObject 호출`을 신경써야 한다. <br>
      기본적으로 `defaultReadObject`를 호출하여 부모 클래스의 상태도 올바르게 역직렬화해야 한다. <br><br>

<br><br>


### 방어적으로 작성된 `readObject()` 예시
```java
import java.io.*;
import java.util.*;

// 취약한 직렬화 예시
public class DefensiveSerializableClass implements Serializable {
   
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int[] numbers;
   
    public DefensiveSerializableClass(String name, int[] numbers) {
        this.name = name;
        this.numbers = numbers.clone();             // 방어적 복사
    }
    
    // 커스텀 직렬화 - numbers 배열의 방어적 복사
    private void writeObject(ObjectOutputStream oos) throws IOException {
        oos.defaultWriteObject();                   // 기본 직렬화 수행
    }
    
    // 커스텀 역직렬화 - 방어적 복사와 유효성 검사
    private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
        ois.defaultReadObject();                    // 기본 역직렬화 수행
        
        // numbers 배열을 방어적으로 복사
        if (numbers == null) {
            throw new InvalidObjectException("numbers 배열은 null이 될 수 없다.");
        }
        
        numbers = numbers.clone();                  // 가변 필드의 방어적 복사
        
        // 유효성 검사 : 배열 크기 확인 (예시로 음수 값이 없어야 함)
        for(int number : numbers) {
            if(number < 0) {
                throw new InvalidObjectException("numbers 배열에 음수 값이 있다.");
            }
        }
    }
    
    @Override
    public String toString() {
        return "DefensiveSerializableClass{name = '" + name + "', numbers = " + toString(numbers) + "}";
    }
   
    public static void main(String[] args) throws IOException, ClassNotFoundException {

        int[] numbers = {1, 2, 3, 4, 5};
        DefensiveSerializableClass obj = new DefensiveSerializableClass("Test", numbers);
        
        // 객체 직렬화
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(baos);
        oos.writeObject(obj);
        
        // 객체 역직렬화
        ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
        ObjectInputStream ois = new ObjectOutputStream(bais);
        DefensiveSerializableClass deserializedObj = (DefensiveSerializableClass) ois.readObject();
        
        System.out.println(deserializedObj);
   }
}
```
위 코드에서 `name`과 `numbers`라는 필드를 가진 객체가 직렬화되고 `numbers` 배열은 가변 객체이다. <br>
`writeObject()`에서는 기본 직렬화 방식을 사용하고 `readObject()`에서 `방어적 복사`를 통해 배열을 복사한다. <br>
이 방식으로 `외부에서 참조하는 객체를 변경할 수 없도록` 보호한다. <br><br>

`readObject()`에서는 `numbers`배열이 `null`이 아닌지, 배열에 음수 값이 없는지 등 `유효성 검사를 추가`하여 <br>
역직렬화 과정에서 데이터가 유효한지 검증한다. 유효하지 않다면 예외를 던진다. 

<br><br>


### 정리하자면
1. `readObject()`를 방어적으로 작성해야 하는 이유는 직렬화된 객체의 데이터가 `신뢰할 수 없거나 손상`되었을 가능성이 있기 때문이다. <br><br>
   
2. `방어적 복사`와 `유효성 검사`를 통해 역직렬화된 객체의 무결성 보안을 보장해야 한다. <br><br>

3. 이를 통해 `보안 취약점`을 줄이고 프로그램의 `안전성`을 높일 수 있다. <br><br>

