# Item 03. private 생성자나 열거 타입으로 싱글톤임을 보증하라

### 내용 요약 <br>
1. 싱글턴 패턴의 필요성
    - 특정 클래스가 `인스턴스를 하나만 가져야 하는 상황`에서 싱글턴 패턴이 필요하다. <br><br>

    - 예를 들어, 시스템에서 `하나의 설정 객체`나 `하나의 로깅 객체`만 있어야 하는 경우가 있다. <br><br>

2. 싱글턴 구현 방법
    - `정적 팩토리 메소드`를 사용하는 방법과 `public static final필드`를 사용하는 방법이 대표적이다. <br>
      하지만 두 방법 모두 `private 생성자를 사용하여` 외부에서 인스턴스를 생성할 수 없도록 막아야 한다. <br><br>

3. 정적 팩토리 메소드를 사용하는 방식
    - 정적 팩토리 메소드는 메소드 이름을 사용할 수 있어 `더 유연한 네이밍`이 가능하다. <br><br>
   
    - 예를 들어, 기존 인스턴스를 반환하거나 필요에 따라 새로운 인스턴스를 반환하는 등의 `유연성을 제공`할 수 있다. <br><br>

4. 열거 타입을 사용한 싱글턴 구현
    - `가장 권장되는 방법`은 열거 타입을 사용하는 것이다. <br>
      자바에서 열거타입은 싱글턴의 특성을 `자동으로 보장`하고 `직렬화`와 `리플렉션 공격`에도 안전하다. <br><br>
   
    - 열거 타입을 사용하면 코드가 간결하고, 자바 언어 차원에서 싱글톤의 `보증과 안전성`을 제공할 수 있다. <br><br>


<br><br>


### public static final 필드를 사용하는 방법
```java
public class Elvis {

    private static final Elvis INSTANCE = new Elvis();
   
    private Elvis() {
        // private 생성자
    }
    
    public void PracticeSingleton() {
        System.out.println("싱글톤 패턴 학습");
    }
}

public class Main {

   public static void main(String[] args) {
      Elvis elvis = Elvis.INSTANCE;
      elvis.PracticeSingleton();
   }
}

```
위 코드에서 `Elvis` 클래스는 싱글톤이다. <br>
정적필드인 `INSTANCE` 에 오직 하나의 인스턴스만 생성된다. <br>
`private 생성자`는 외부에서 인스턴스를 생성하지 못하도록 막아준다. <br>
`leaveTheBuilding()` 메소드는 싱글톤 인스턴스를 통해 호출할 수 있다. <br>
위 코드의 결과로 `싱글톤 패턴 학습`이 출력된다. <br>

<br><br>


### 정적 팩토리 메소드를 사용하는 방법
```java
public class Elvis {
    
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() {
        // private 생성자
    }
    
    public static Elvis getInstance() {
        return INSTANCE;
    }
    
    public void PracticeStaticFactoryMethod() {
        System.out.println("정적 팩토리 메소드 연습");
    }
}

public class Main {

   public static void main(String[] args) {
      Elvis elvis = Elvis.getInstance();
      elvis.PracticeStaticFactoryMethod();
   }
}
```
위 코드에서 `getInstance()` 메소드를 통해 싱글톤 인스턴스를 반환한다. <br> 
이 방법은 `정적 팩토리 메소드`를 사용하여 인스턴스를 반환하므로, 메소드 이름을 `유연하게 변경`하거나 `메소드 로직`을 추가할 수 있는 장점이 있다. <br><br>

<br><br>

### 열거 타입을 사용하는 방법
```java
public enum Elvis {
    
    INSTANCE;
    
    public void PracticeEnumType() {
        System.out.println("열거 타입 연습");
    }
}

public class Main {

   public static void main(String[] args) {
      Elvis elvis = Elvis.INSTANCE();
      elvis.PracticeEnumType();
   }
}
```
위 코드에서 `Elvis` 클래스는 `열거 타입`으로 정의되었으며, `INSTANCE`는 이 타입의 유일한 인스턴스이다. <br>
자바의 열거 타입은 `싱글톤을 보장`하고, `직렬화`와 `리플렉션 공격`에도 안전하다. <br>
`가장 간결하고 안전한 싱글톤 구현`방식이다. <br>

<br><br>


### 정리하자면
1. `정적 필드`를 이용한 방식은 간단하게 싱글톤을 구현할 수 있지만, 직렬화와 리플렉션 공격에서 안전성을 보장하기 위해 추가적인 코드가 필요하다.
2. `정적 팩토리 메소드`를 사용하는 방식은 `유연성`을 제공하지만 구현 방식은 비숫하다.
3. `열거 타입을 사용한 싱글톤 구현`은 자바에서 싱글톤 패턴을 구현할 때 `가장 권장되는 방법`으로 코드가 간결하고 `직렬화 및 리플렉션 공격에도 안전`하다. 
4. 필요에 따라 `정적 팩토리 메소드`를 사용하여 더 유연한 싱글턴 구현도 가능하다.


