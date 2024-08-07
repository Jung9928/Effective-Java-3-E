# Item 29. 이왕이면 제네릭타입으로 만들라

### 내용 요약 <br>
1. `제네릭`타입의 장점
    - 타입 안정성
        - 제네릭 타입을 사용하면 컴파일 타임에 타입 검사를 수행하므로, 타입 관련 오류를 런타임에서 방지할 수 있다. <br><br>

    - 재사용성
        - 제네릭 타입은 다양한 타입에 대해 재사용할 수 있으며, 코드의 중복을 줄여준다. <br><br>

    - 명시적 타입 표현
        - 제네릭 타입은 클래스 or 인터페이스의 타입을 명확히 표현하여 코드의 가독성과 유지보수성을 높인다. <br><br>


2. `제네릭`타입 정의
    - 제네릭 타입을 정의할 때, 클래스나 인터페이스 선언에 타입 파라미터를 추가한다. 예를 들어, `class Box<T>`와 같은 형태로 정의할 수 있다. <br><br>
    - 제네릭 타입은 타입 파라미터를 통해 다양한 타입의 객체를 처리할 수 있게 해준다. <br><br>

3. `제네릭`타입의 예시
   - 제네릭 타입을 사용하여 타입 안정성을 보장하고, 다양한 타입에 대해 일관된 방식으로 작업할 수 있다. <br><br>


### `제네릭`타입 정의 & 사용 예
```java

// 제네릭 타입 정의
public class Box<T> {
    
   private T value;
   
   public void set(T value) {
     this.value = value;
   }
   
   public T get() {
     return value;
   }
    
   public static void main(String[] args) {
      Box<String> stringBox = new Box<>();
      stringBox.set("Hello World");
      String value = stringBox.get();
      System.out.println("String value : " + value);        // String value : Hello World
      
      Box<Integer> integerBox = new Box<>();
      integerBox.set(123);
      Integer intValue = integerBox.get();
      System.out.println("Integer value : " + intValue);    // Integer value : 123
   }
}
```

<br>

### 정리하자면
`제네릭`타입을 사용하면 클래스나 인터페이스의 타입을 매개변수화하여 다양한 타입에 대해 안전하게 작업할 수 있다. <br>
`제네릭`타입을 통해 타입 안정성을 보장하고 코드의 재사용성과 유지보수성을 높일 수 있다. <br>

<br>