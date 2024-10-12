# Item 85. 자바 직렬화의 대안을 찾으라

### 내용 요약 <br>
1. 자바 직렬화의 문제점
    - `보안 취약점`
      - 직렬화된 객체는 역직렬화 과정에서 `임의의 코드 실행 공격`에 노출될 수 있다. <br>
        공격자가 조작된 직렬화 스트림을 제공하여 시스템에서 악의적인 코드를 실행할 수 있다. <br><br>

    - `버그와 안정성 문제`
      - 직렬화된 형태는 객체의 내부 표현에 의존하므로, 클래스의 구현이 변경되면 호환성이 깨질 수 있다. <br>
        이는 역직렬화 과정에서 `InvalidClassException` 등의 예외를 발생시킬 수 있다. <br><br>

    - `유지보수 어려움`
      - 직렬화 형태는 클래스의 private 필드까지 포함하므로, 클래스의 내부 구현을 변경하면 직렬화 호환성이 깨질 수 있어 `유지보수가 어렵다` <br><br>

    - `성능 저하`
      - 직렬화는 일반적인 데이터 전송 방식보다 `성능이 떨어질 수 있다` <br>
        불필요한 데이터까지 포함되기 때문이다. <br><br>


2. 직렬화의 대안 찾기
    - `직렬화를 피할 수 있는 설계 고려`
        - 객체의 상태를 저장하거나 전송해야 한다면, `직렬화를 사용하지 않는 방식을 고려`헤야 한다. <br><br>

    - `커스텀 포맷 사용`
        - JSON, XML, 프로토콜 버퍼(Protocol Buffers) 등의 `표준화된 데이터 포맷`을 사용하여 객체를 변환하고 전송하는 것이 좋다. <br><br>

    - `데이터 전송 객체 (Data Transfer Object, DTO)`
        - 데이터만을 포함하는 개체로 전송하고, 그 안의 필드를 직렬화하여 전송하는 방식으로, 직렬화에 의존하지 않는 설계를 유지할 수 있다. <br><br>

    - `커스텀 직렬화 방식`
        - 자바 직렬화를 직접 사용하지 않고, `Serializable`을 구현하지 않는 방식으로, 직렬화 논리를 수동으로 구현하여 <br>
          직렬화 포맷을 정의하는 방법도 고려할 수 있다. <br><br>

    - `자바 직렬화를 피하기 어려운 경우`
        - 객체 검증을 추가하거나 `readObject()`메소드를 커스터마이즈하여 안전성을 강화해야 한다. <br><br>

3. 역직렬화 위험 완화 방법
    - `Serializable 구현 피하기`
      - 클래스가 직렬화되지 않도록 `Serializable` 언터페이스 구현을 피한다. <br><br>

    - `역직렬화 필터링 사용`
      - 반드시 직렬화를 사용해야 한다면, `역직렬화 과정에서 데이터를 검증`하는 필터를 사용하여 위험을 완화할 수 있다. <br><br>


4. 신뢰할 수 없는 데이터의 역직렬화 금지
    - 외부에서 입력된 신뢰할 수 없는 데이터에 대해 `역직렬화를 수행하지 말아야 한다` <br>
      이는 보안 공격의 주요 통로가 될 수 있다. <br><br>

      
<br><br>




### 자바 직렬화 사용의 문제점
```java
import java.io.*;

// 취약한 직렬화 예시
public class User implements Serializable {
   
    private static final long serialVersionUID = 1L;
    private String username;
    private transient String password;                  // 직렬화되지 않지만 여전히 안전하지 않음
    
   
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
    
    // 기본적인 직렬화 사용 예시
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        
        User user = new User("john_doe", "secure_password");
        
        // 객체 직렬화
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("user.ser"))) {
            oos.writeObject(user);
        }
        
        // 객체 역직렬화
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("user.ser"))) {
            User deserializedUser = (User) ois.readObject();
            System.out.println("Username : " + deserializedUser.username);
            System.out.println("Password : " + deserializedUser.password);              // null 출력됨
        }
   }
}
```
위 코드에서 `Serializable` 인터페이스를 구현한 `User` 클래스의 객체를 직렬화하고 파일로 저장한 후, 다시 역직렬화하고 있다. <br>
`password` 필드는 `transient`로 선언되어 직렬화되지 않지만, 여전히 보안에 취약할 수 있다. <br>
또한, 클래스가 진화(ex : 필드가 추가되거나 제거됨)하는 과정에서 이전 직렬화된 객체들과 호환성 문제를 일으킬 수 있다. <br><br>

<br><br>



### 직렬화를 피하고 JSON을 사용하는 대안
```java
import com.google.gson.Gson;

// JSON 직렬화 대안 예시
public class UserDTO {
    
    private String username;
    private String password;
    
   
    public UserDTO(String username, String password) {
        this.username = username;
        this.password = password;
    }
    
    // JSON 직렬화 사용 예시
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        
        UserDTO user = new UserDTO("john_doe", "secure_password");
        Gson gson = new Gson();
        
        // 객체를 JSON으로 직렬화
        String json = gson.toJson(user);
        System.out.println("Serialized JSON : " + json);
        
        // JSON을 객체로 역직렬화
        UserDTO deserializedUser = gson.fromJson(json, UserDTO.class);
        System.out.println("Username : " + deserializedUser.username);
        System.out.println("Password : " + deserializedUser.password);
   }
}
```
위 코드에서 `Gson` 라이브러리를 사용하여 `UserDTO` 객체를 JSON으로 직렬화하고 다시 역직렬화한다. <br>
이 방식은 자바 직렬화에 의존하지 않으며 보안이 향상되고 데이터 포맷이 더 명확하다. <br>
JSON은 사람이 읽을 수 있고, 다른 플랫폼과 쉽게 호환될 수 있다. <br><br>

<br><br>


### 정리하자면
1. 자바 직렬화는 보안과 유지보수에 심각한 문제를 일으킬 수 있기 때문에 가능한 한 사용을 피해야 한다. <br><br>
2. JSON, XML, Protocol Buffers 등의 `대체 직렬화 방식`을 사용하는 것이 더 안전하고 효율적이다. <br><br>
3. 직렬화를 반드시 사용해야 하는 경우, `직렬화 프로세스를 안전하게 관리`하고 보안 조치를 강화해야 한다. 