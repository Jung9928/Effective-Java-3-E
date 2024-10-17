# Item 89. 인스턴스 수를 통제해야 한다면 readResolve보다는 열거 타입을 사용하라

### 내용 요약 <br>
1. 인스턴스 수 통제의 필요성
    - 특정 클래스에서 `단 하나의 인스턴스` 또는 제한된 수의 인스턴스만 존재하도록 강제해야 할 경우가 있다. <br>
      예를 들어, `싱글톤 패턴`은 시스템에서 하나의 인스턴스만 존재하도록 보장한다. <br><br>

2. `readResolve` 메소드를 통한 인스턴스 수 통제
    - 직렬화된 객체가 역직렬화될 때, 인스턴스 수를 통제하기 위해 `readResolve` 메소드를 사용할 수 있다. <br><br>
    - 그러나 `readResolve`를 사용하면 직렬화 및 역직렬화 과정에서 개발자가 `구현을 신경 써야`하며 `버그`가 발생할 가능성이 있다. <br>
      특히, `직렬화 프록시 패턴`이 제대로 구현되지 않으면 시스템에 여러 인스턴스가 생성될 수 있다. <br><br>

3. 열거 타입(enum)을 통한 인스턴스 수 통제
    - 열거 타입은 자바에서 본질적으로 `싱글톤을 보장`한다. <br>
      즉, 열거 타입은 자바 플랫폼에 의해 `인스턴스가 하나`만 존재하도록 설계되어 있다. <br><br>

    - 열거 타입을 사용하면 직렬화 과정에서 `자동으로 인스턴스가 보존`되기 때문에 추가적인 코딩 작업이 필요하지 않다. <br> 
      이는 직렬화와 역직렬화 시 `더 안전하고 간단한 방식`이다. <br><br>

    - 따라서 열거 타입을 사용하면 `readResolve`와 같은 메소드를 추가할 필요 없이 `코드가 더 간결`해지고 `버그 발생 가능성`도 낮아진다. <br><br>


4. 싱글톤 구현 시의 열거 타입 활용
    - 싱글톤 패턴을 구현할 때 `열거 타입을 사용하면` 별도의 동기화나 복잡한 논리를 작성할 필요 없이 간단하게 싱글톤을 만들 수 있다. <br><br>

   
<br><br>




### `readResolve`를 사용하는 싱글톤 패턴
```java
import java.io.*;

// 취약한 직렬화 예시
public class Singleton implements Serializable {
   
    private static final Singleton INSTANCE = new Singleton();
   
    public Singleton() {}
    
   public static Singleton getInstance() {
        return INSTANCE;
   }
   
   // readResolve 메소드로 싱글톤 보장
   private Object readResolve() throws ObjectStreamException {
        return INSTANCE;
   }
   
    
   public static void main(String[] args) throws IOException, ClassNotFoundException {
        
        Singleton instance1 = new Singleton.getInstance();
        
        // 객체 직렬화
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(baos);
        oos.writeObject(instance1);
        
        // 객체 역직렬화
        ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bais);
        Singleton instance2 = (Singleton) ois.readObject();
        
        // 동일한 인스턴스인지 확인
        System.out.println(instance1 == instance2);     // true
   }
}
```
위 코드에서 `Singleton` 클래스는 `readResolve` 메소드를 통해 직렬화된 객체가 역직렬화된 후에도 `하나의 인스턴스만` 존재하도록 보장한다. <br><br>

`readResolve`메소드는 역직렬화 시 새로운 객체를 만드는 대신 기존 인스턴스 (INSTANCE)를 반환한다.

<br><br>



### 열거 타입을 사용하는 싱글톤 패턴
```java
import com.google.gson.Gson;

// JSON 직렬화 대안 예시
public enum SingletonEnum {
    
    INSTANCE;
   
    public void doSomething() {
        System.out.println("싱글톤 작업 수행");
    }
    
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        SingletonEnum instance1 = SingletonEnum.INSTANCE;
        SingletonEnum instance2 = SingletonEnum.INSTANCE;
        
        // 동일한 인스턴스인지 확인
        System.out.println(instance1 == instance2);                 // true
    }
}
```
위 코드에서 `SingletonEnum`은 열거 타입으로 정의된 싱글톤 패턴이다. <br><br>
열거 타입은 `자동으로 인스턴스 수가 하나로 보장`되며 추가적인 직렬화 처리 코드를 작성할 필요가 없다. <br><br>
열거 타입은 자바 언어 차원에서 단 하나의 인스턴스만을 허용하기 때문에 `더 간단하고 안전한 방식`이다. 

<br><br>


### 정리하자면
1. `인스턴스 수를 통제`해야 한다면 `readResolve`를 통한 구현보다는 `열거 타입(enum)`을 사용하는 것이 더 안전하고 간결하다
2. 열거 타입은 `싱글톤을 자연스럽게 구현`하며, 직렬화와 역직렬화에서도 자동으로 `인스턴스 수를 보장`해준다. 
3. 열거 타입을 사용하면 `직렬화 관련 버그`를 줄이고 더 직관적이며 유지보수하기 쉬운 코드를 작성할 수 있다.  