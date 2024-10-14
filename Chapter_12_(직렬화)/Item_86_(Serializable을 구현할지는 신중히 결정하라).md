# Item 86. Serializable을 구현할지는 신중히 결정하라

### 내용 요약 <br>
1. Serializable의 위험성
    - `호환성 유지의 어려움`
        - `Serializable`을 구현하면 클래스의 `직렬화된 형식(객체의 이진표현)`을 유지해야 하며 <br>
          `직렬화된 객체와의 호환성`을 계속 유지해야 한다. <br>
          이로 인해, 클래스에 필드를 추가하거나 제거할 때 큰 제약이 따른다. <br><br>

    - `보안 문제`
        - 직렬화된 데이터를 신뢰하지 않으면 악의적인 데이터에 의해 시스템이 `보안 공격을 받을 가능성`이 있다. <br>
          직렬화된 `외부 입력을 검증`할 수 없기 때문에, 공격자가 의도적으로 조작된 객체 그래프를 전송할 수 있다. <br><br>

    - `성능 비용`
        - 직렬화는 성능에 부담을 준다. <br>
          특히, `객체 그래프를 탐색`하고 그 객체들을 직렬화하는 과정을 `비용이 크고` 시스템 자원을 많이 소모한다. <br>
    

2. Serializable의 사용을 피해야 하는 이유
    - 일반적으로 `Serializable`을 구현하지 않는 것이 더 안전하다. <br>
      시스템 간 데이터 전송이나 저장 시, 다른 방법을 사용하는 것이 좋다. <br>
      JSON이나 XML같은 텍스트 기반 포맷을 사용하거나, 객체를 데이터베이스에 저장하는 방식이 더 권장된다. <br><br>

    - `인터페이스 설계 시 주의`
        - 만약 인터페이스에 `Serializable`을 구현하도록 명시하면, 그 인터페이스를 구현하는 모든 클래스가 직렬화 가능해야 한다. <br>
          이는 불필요하게 직렬화의 복잡성을 유발할 수 있다. <br><br>

3. 불가피하게 Serializable을 구현해야 하는 경우
    - `명시적 serialVersionUID` 사용
        - `Serializable`을 구현하는 클래스는 `serialVersionUID`를 명시적으로 선언하는 것이 중요하다. <br>
          `serialVersionUID`는 클래스의 직렬화 형식을 식별하는 고유 ID로, 클래스의 수정이 이루어질 때 `직렬화된 데이터의 호환성 문제를 방지`할 수 있다. <br><br>

    - `객체 검증 및 보안 조치`
        - 직렬화된 데이터를 처리할 때, `역직렬화 과정엣어 데이터를 검증`헤야 한다. <br>
          예를 들어, `readObject()`메소드를 재정의하여 객체의 상태를 확인하거나 필수적인 보안 검사를 수행하는 것이 중요하다. <br><br>

          
<br><br>


### `Serializable`을 구현할 때의 주의사항
```java
import java.io.*;

public class Employee implements Serializable {
   
    // serialVersionUID를 명시적으로 선언하여 클래스의 직렬화 형식을 명시
    private static final long serialVersionUID = 1L;
   
    private String name;
    private int age;
    
    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 명시적으로 역직렬화 시 검증하는 readObject 메소드
    private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
        s.defaultReadObject();
        
        // 역직렬화된 객체의 상태를 검증
        if(age < 0) {
            throw new IllegalArgumentException("나이는 음수가 될 수 없다 : " + age);
        }
    }
    
    @Override
    public String toString() {
        return "Employee{name = '" + name + "', age = " + age + "}";
    }
    
    public static void main(String[] args) {
        Employee employee = new Employee("John Doe", 30);
        System.out.println(employee);
   }
}
```
위 코드에서 `Employee` 클래스가 `Serializable`을 구현하고 있으며, `serialVersionUID`를 명시적으로 선언하여 직렬화된 객체와 클래스 수정 사이의 호환성을 유지한다. <br>
또한, `readObject()` 메소드는 재정의하여 역직렬화 과정에서 객체의 상태를 검증하고 유효하지 않은 값이 들어오는 경우 예외를 던진다. <br>
이렇게 하면 보안과 데이터 무결성을 보장할 수 있다. <br><br>

<br><br>


### 정리하자면
1. `Serializable`을 구현할 때는 신중하게 결정해야 하며, 직렬화는 단순히 데이터를 전송하는 메커니즘 그 이상으로 `보안`, `성능`, `유지보수성`에 영향을 미친다. <br><br>
2. 직렬화의 대안을 항상 고려하고 불가피하게 사용할 경우에도 `직렬화 형식의 호환성`과 `보안`에 대한 철저한 관리가 필요하다.