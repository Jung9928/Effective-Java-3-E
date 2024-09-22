# Item 65. 리플렉션보다는 인터페이스를 사용하라

### 내용 요약 <br>
1. 리플렉션이란
    - 리플렉션은 `런타임`에 `클래스`, `메소드`, `필드` 등에 접근할 수 있게 해주는 Java API다. <br>
      컴파일 시점이 아닌 런타임에 동적으로 코드를 다룰 수 있어 유연성이 높지만 잘못 사용하면 많은 문제를 일으킬 수 있다. <br><br>

2. 리플렉션의 단점
    - `컴파일 타임 타입 안정성`이 보장되지 않아, 잘못된 사용 시 런타임에 `예외`가 발생할 수 있다. <br><br>
    - 성능 저하가 있을 수 있다. <br>
      리플렉션은 직접 메소드 호출보다 더 느리고, 반복적으로 사용하면 성능이 크게 저하될 수 있다. <br><br>
    - 코드가 복잡하고 유지보수가 어렵다. 리플렉션으로 작성된 코드는 직관적이지 않고 읽기 어렵다. <br><br>
      
3. 리플렉션은 제한된 상황에서만 사용
    - 리플렉션은 객체를 동적으로 생성하거나, 런타임에 모르는 클래스를 다룰 때처럼 `특정 상황에서만 사용`해야 한다. <br><br>

4. 대안 : 인터페이스 사용
    - 리플렉션보다는 `컴파일 타임에 타입이 확정되는 인터페이스`를 사용하는 것이 더 안전하고 명확하며 성능에도 좋다. <br><br>


<br><br>

### 리플렉션을 사용한 예시
```java
import java.lang.reflect.Method;

public class Item65Example {

    public static void main(String[] args) {
        
        // 리플렉션을 통해 클래스와 메소드에 접근
        Class<?> c = Class.forName("java.util.Random");
        Object randomInstance = c.getDeclaredConstructor().newInstance();
        
        Method nextIntMethod = c.getMethod("nextInt", int.class);
        int randomValue = (int) nextIntMethod.invoke(randomInstance, 100);
        System.out.println("Random Value : " +randomValue);
    }
}
```
위 코드에선 `java.util.Random` 클래스의 `nextInt` 메소드를 리플렉션을 통해 호출한다. <br>
`Class.forName()`을 사용해 클래스 정보를 동적으로 로드하고, `invoke` 메소드로 메소드를 실행한다. <br><br>

하지만 이 방식은 `컴파일 타임 타입 안정성`이 없으며, 리플렉션 코드가 더 복잡하고 직관적이지 않다는 단점이 있다. <br>
또한, 잘못된 메소드를 호출하려 하면 `런타임에 예외`가 발생한다. <br><br>

<br><br>


### 인터페이스를 사용한 대안 예시
```java
import java.util.Random;

public class Item65Example {

    public static void main(String[] args) {
        
        // 인터페이스로 클래스 사용
        Random random = new Random();
        int randomValue = random.nextInt(100);
        System.out.println("Random Value : " + randomValue);
    }
}
```
위 코드에선 리플렉션을 사용하지 않고 `Random`객체를 직접 생성하여 메소드를 호출한다. <br>
코드가 간단하고 `타입 안정성`이 보장된다. 리플렉션을 사용하지 않아 성능도 더 좋고, 코드의 가독성도 높다. <br><br>

<br><br>

### 리플렉션 사용 후 인터페이스로 캐스팅
```java
import java.lang.reflect.Constructor;

interface MyInterface {
    void doSomething();
}

class MyClass implements MyInterface {
    public void doSomething() {
        System.out.println("Doing something!");
    }
}

public class Item65Example {

    public static void main(String[] args) {
        
        // 리플렉션을 사용해 객체 생성
        Constructor<?> constructor = Class.forName("MyClass").getConstructor();
        MyInterface myObject = (MyInterface) constructor.newInstance();             // 인터페이스로 캐스팅
        myObject.doSomething();                                                     // 안전하게 메소드 호출
    }
}
```
위 코드에선 리플렉션을 사용해 `MyClass` 객체를 생성하고, 이를 `MyInterface`로 캐스팅하여 메소드를 호출한다. <br>
리플렉션을 사용한 후, `인터페이스로 캐스팅`함으로써 `타입 안전성`을 확보한다. <br><br>
이를 통해, 리플렉션의 유연성과 인터페이스의 안전성을 함께 얻을 수 있다. <br><br>


### 정리하자면
1. 리플렉션은 런타임에 동적으로 객체나 메소드에 접근할 수 있는 강력한 도구이지만, 성능저하, 타입 안전성 결여, 복잡성 증가 등 여러 단점이 있다. <br>
2. `리플렉션은 필요한 경우에만 제한적으로 사용`하고 대부분의 상황에서는 `인터페이스`를 사용하여 명시적인 설계를 유지하는 것이 더 좋다. <br>
3. 리플렉션을 사용해야 할 경우에도, 객체를 인터페이스나 추상 클래스로 캐스팅하여 타입 안정성을 확보하는 것이 더 좋다. 


