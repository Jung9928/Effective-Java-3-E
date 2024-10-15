# Item 87. 커스텀 직렬화 형태를 고려해보라

### 내용 요약 <br>
1. 기본 직렬화의 한계
    - 기본적으로 자바의 `Serializable` 인터페이스를 구현하면 JVM이 자동으로 객체의 모든 필드를 직렬화한다. <br>
      그러나 `불필요한 필드`까지 직렬화되어 성능에 악영향을 미치거나 `보안 위험`을 초래할 수 있다. <br><br>

    - 클래스가 수정되면서 직렬화된 객체와의 `호환성 문제가 발생`할 수 있다. <br><br>

2. 커스텀 직렬화의 필요성
    - 커스텀 직렬화를 사용하면 `효율성`을 높이고 `보안`을 강화할 수 있다. <br>
      예를 들어, `캐시된 갑시안 파생 값`과 같은 정보는 직렬화에서 제외할 수 있다. <br><br>

    - 또한, `필드 값 검증` 및 `보안 검토`를 포함해 직렬화 형식을 명확히 제어할 수 있다. <br><br>


3. 커스텀 직렬화의 구현
    - 커스텀 직렬화는 `writeObject()`와 `readObject()` 메소드를 재정의하여 구현한다. <br>
      이 메소드를 사용하면 원하는 필드만 선택적으로 직렬화할 수 있다. <br><br>

    - 이러한 커스텀 직렬화 형태는 `유연성`을 제공하면서도 `성능 최적화` 및 `보안성`을 높일 수 있는 방법이다. <br><br>


4. 직렬화 가능한 필드와 비직렬화 필드를 분리
    - 일부 필드는 직렬화하지 않아야 할 필요가 있을 수 있다. `transient` 키워드를 사용하여 직렬화하지 않을 필드를 선언하고 <br>
      필요할 때 `writeObject()`와 `readObject()`에서 이 필드들을 다시 계산하거나 재설정할 수 있다. <br><br>


5. serialVersionUID의 역할
   - 커스텀 직렬화에서도 `serialVersionUID`를 명시적으로 정의해야 하며, 이는 클래스의 직렬화 형식을 정의하는 고유 식별자로 클래스 변경 시, `호환성` 문제를 예방한다. <br><br>

<br><br>


### 커스텀 직렬화 형태 구현
```java
import java.io.*;

// 취약한 직렬화 예시
public class CustomSerializableClass implements Serializable {
   
    private static final long serialVersionUID = 1L;
    
    private String username;
    private transient int cachedValue;                  // 직렬화하지 않는 필드
   
    public CustomSerializableClass(String username, int cachedValue) {
        this.username = username;
        this.cachedValue = cachedValue;
    }
    
    // 커스텀 직렬화 - 선택적으로 필드를 직렬화
    private void writeObject(ObjectOutputStream oos) throws IOException {
        oos.defaultWriteObject();                   // 기본 직렬화 수행
        oos.writeInt(calculateCachedValue());       // 필요한 데이터만 직렬화
    }
    
    // 커스텀 역직렬화 - 선택적으로 필드를 역직렬화하고 필드 초기화
    private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
        ois.defaultReadObject();                    // 기본 역직렬화 수행
        cachedValue = ois.readInt();                // 캐시된 값을 복원
    }
    
    // 캐시된 값을 계산하는 메소드
    private int calculateCachedValue() {
        return name.length();                       // 예시로 name 길이를 캐시로 사용
    }
    
    @Override
    public String toString() {
        return "CustomSerializableClass{name = '" + name + "', cachedValue = " + cachedValue + "}"; 
    }
   
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        
        CustomSerializableClass obj = new CustomSerializableClass("Test", 0);
        
        // 객체 직렬화
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(baos);
        oos.writeObject(obj);
        
        // 객체 역직렬화
        ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
        ObjectInputStream ois = new ObjectOutputStream(bais);
        CustomSerializableClass deserializedObj = (CustomSerializableClass) ois.readObject();
        
        System.out.println(deserializedObj);
   }
}
```
위 코드에서 `name`필드는 직렬화되고, `cachedValue`는 `transient`로 선언되어 직렬화되지 않는다. <br>
`writeObject()`에서 `defaultWriteObject()`를 사용해 기본적인 직렬화는 수행하되, <br>
`cachedValue` 대신 필요 시 `calculateCachedValue()`를 호출해 그 값을 별도로 직렬화한다. <br>
`readObject()`에서 `cachedValue`를 복원하는 방식으로 커스텀 역직렬화가 수행된다. <br>
이 방법으로 `필요한 데이터만 직렬화`하고 `불필요한 필드는 제외`하여 성능을 최적화하고 보안을 강화할 수 있다. <br><br>

<br><br>


### 정리하자면
1. `커스텀 직렬화`는 성능과 보안 면에서 `기본 직렬화보다 우수`한 결과를 제공한다. <br>
   필요한 경우, 커스텀 직렬화 방식으로 직렬화할 필드를 선택하거나, 불필요한 데이터를 직렬홯에서 제외할 수 있다. <br><br>
2. 특히, `직렬화 형식의 변경 가능성`이 있거나 `민감한 정보나 캐시된 값`을 포함한 클래스에서 커스텀 직렬화를 고려하는 것이 중요하다. <br><br>
