# Item 62. 다른 타입이 적절하다면 문자열 사용을 피하라 

### 내용 요약 <br>
1. `문자열을 다른 용도로 사용하지 마라`
    - 문자열을 텍스트가 아닌 용도로 사용하면 오동작하거나 코드가 복잡해진다. <br><br>

    - 문자열을 `열거 타입`, `정수형`, `Boolean 값` 등의 용도로 사용하는 것은 잘못된 사용 사례이다. <br><br>


2. `문자열로 열거 타입을 흉내 내지 마라`
    - 열거형 값을 표현할 때 문자열 대신 `enum`을 사용하는 것이 좋다. <br>
      문자열을 사용하면 오타나 잘못된 값을 허용하게 되고, `컴파일 시점`에 오류를 발견할 수 없다. <br><br>


3. 문자열로 혼합 타입을 사용하지 마라
    - 여러 값을 하나의 문자열에 저장하는 방식(ex: `id:name`)은 데이터 관리와 수정이 어려워진다. <br>
      이를 대신해 데이터 객체나 클래스를 사용하는 것이 좋다. <br><br>


4. 문자열로 권한을 처리하지 마라
    - 권한이나 상태를 문자열로 저장하는 대신 `boolean`, `enum` 또는 `권한 관련 객체`를 사용하는 것이 더 적절하다. <br><br>

   
5. 정확한 표현을 위해 적절한 타입 사용
    - 문자열을 남용하지 않고, 표현하고자 하는 값에 적합한 타입을 사용하면 코드의 가독성과 안정성이 크게 향상된다. <br><br>

<br><br>

### 잘못된 예시 : 문자열을 열거형으로 사용
```java
public class Item62Example {
    
    private static final String STATUS_RUNNING = "RUNNING";
    private static final String STATUS_STOPPED = "STOPPED";
    
    private String status;
    
    public void setStatus(String status) {
        this.status = status;
    }
    
    public boolean isRunning() {
        return STATUS_RUNNING.equals(status);
    }
    
    public static void main(String[] args) {
        
        Item62Example example = new Item62Example();
        example.setStatus("RUNNING");
        
        // 오타나 잘못된 값에 대한 검증이 없음
        System.out.println(example.isRunning());               // true
    }
}
```
위 코드에선 문자열을 `상태를 나타내는 열거형`으로 사용하고 있다. <br>
하지만 문자열을 사용하면 오타나 잘못된 값(`Running` 대신 `Runing` 등)을 사용하더라도 `컴파일 타임`에 오류가 발생하지 않고 `런타임`에만 문제가 드러난다. <br>
또한, 문자열 상수를 관리하는데도 어려움이 있다. 

<br><br>



### 올바른 예시 : 열거 타입을 사용
```java
public class Item62Example {
    
    public enum Status {
        RUNNING, STOPPED
    }
    
    private Status status;
    
    public void setStatus(Status status) {
        this.status = status;
    }
    
    public boolean isRunning() {
        return status == Status.RUNNING;
    }
    
    public static void main(String[] args) {
        
        Item62Example example = new Item62Example();
        example.setStatus(Status.RUNNING);
        
        // 상태를 열거형으로 관리하므로 오타나 잘못된 값을 허용하지 않음
        System.out.println(example.isRunning());               // true
    }
}
```
`열거 타입`을 사용하면 `컴파일 타임`에 오류를 검출할 수 있고, 상태가 명확하게 정의되므로 코드를 관리하고 읽기가 훨씬 수월하다. <br>
상태 값이 문자열처럼 임의로 변경될 가능성도 없으며 실수할 확률이 줄어든다. 

<br><br>





### 잘못된 예시 : 문자열로 여러 값을 표현
```java
public class Item62Example {
    
    // 문자열을 사용해 ID와 이름을 함께 저장
    private String personData = "1234:JohnDoe";
    
    public String getPersonId() {
        return personData.split(":")[0];
    }
    
    public String getPersonName() {
        return personData.split(":")[1];
    }
    
    public static void main(String[] args) {
        
        Item62Example example = new Item62Example();
        
        System.out.println("ID : " +example.getPersonId());
        System.out.println("Name : " +example.getPersonName());
    }
}
```
위 코드에선 `ID`와 `이름`을 하나의 문자열에 저장한 후, 이를 `split`으로 나누어 사용하고 있다. <br>
이 방식은 유지보수성이 떨어지고 데이터를 잘못 처리할 가능성이 높다. <br>
데이터 형식이 변경되면 모든 관련 코드를 수정해야 하며, 실수가 발생할 수 있다

<br><br>





### 올바른 예시 : 클래스를 사용하여 여러 값을 표현
```java
public class Item62Example {
    
    // Person 클래스를 사용하여 ID와 이름을 저장
    public static class Person {
        private final String id;
        private final String name;
        
        public Person(String id, String name) {
            this.id = id;
            this.name = name;
        }
        
        public String getId() {
            return id;
        }
        
        public String getName() {
            return name;
        }
    }
    
    public static void main(String[] args) {
        
        Person person = new Person("1234", "John Doe");
        
        System.out.println("ID : " +person.getId());
        System.out.println("Name : " +person.getName());
    }
}
```
위 코드에선 `Person`클래스를 사용하면 각 필드가 명확하게 정의되고, 데이터를 다루기가 훨씬 쉬워진다. <br>
또한, 데이터 구조가 변경되어도 클래스만 수정하면 되기 때문에 유지보수성이 높아진다. <br><br>

<br><br>







