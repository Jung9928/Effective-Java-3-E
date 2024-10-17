# Item 90. 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라

### 내용 요약 <br>
1. 직렬화의 위험성
    - 객체를 직렬화하고 역직렬화할 때는 `클래스의 불변성을 깨뜨리거나 보안 문제`를 일으킬 수 있다. <br>
      역직렬화 과정에서 생성자가 호출되지 않기 때문에 `객체 상태를 복구하는 과정`에서 오류가 발생할 가능성이 있다. <br><br>


2. 직렬화 프록시 패턴의 장점
    - `직렬화 프록시 패턴`은 복잡한 객체를 직렬화할 때 발생할 수 있는 문제들을 해결하는 `강력한 방법`이다. <br><br>

    - `직렬화 프록시`는 객체의 직렬화를 대신 수행하는 `단순한 데이터 전송 객체(DTO)`와 비슷한 역할을 한다. <br>
      이 프록시는 원본 객체의 상태를 저장하고 역직렬화될 때 다시 원본 객체를 안전하게 복원할 수 있다. <br><br>

3. 기본 동작 방식
    - 객체 직렬화 시, 원본 객체 대신 `프록시 객체`를 직렬화하고, 이 프록시 객체는 원본 객체의 데이터를 저장한다. <br><br>

    - 역직렬화 될 때, 프록시 객체가 원본 객체를 다시 생성하고 반환한다. <br><br>
   
    - 이를 통해 `역직렬화 과정에서 원하지 않는 상태 변화`를 방지할 수 있다. <br><br>


4. 직렬화 프록시 패턴의 사용 조건
    - 이 패턴은 주로 `불변 객체`에서 유용하다. <br>
      불변 객체는 상태가 변하지 않기 때문에 직렬화와 역직렬화 시 `객체의 불변성을 유지`할 수 있다. <br><br>
   
    - 하지만 이 패턴은 `상속 관계에서 사용하기 어렵거나 복잡`할 수 있다. <br>
      상속 구조가 복잡한 클래스에서 프록시 패턴의 이점을 누리기 어렵다. <br><br>


<br><br>




### 직렬화 프록시 패턴을 사용하지 않은 경우
```java
import java.io.*;

public class Period implements Serializable {
    
    private final Date start;
    private final Date end;
   
    public Period(Date start, Date end) {
        if(start.after(end))
            throw new IllegalArgumentException("시작 시간이 종료 시간 이후일 수 없다.");
        
        this.start = start;
        this.end = end;
    }
    
    public Date getStart() {
        return start;
    }
    
    public Date getEnd() {
        return end;
    }
}
```
위 코드에서 `Period` 클래스는 직렬화가 가능한 불변 객체이다. <br>
하지만 이 클래스는 `역직렬화 공격`에 취약하다. 악의적인 사용자가 `start`와 `end` 필드를 조작할 수 있어, 객체가 본래의 상태와 다르게 생성될 수 있다. <br><br>

<br><br>



### 직렬화 프록시 패턴을 사용한 경우
```java
import java.io.*;

public class Period implements Serializable {

   private final Date start;
   private final Date end;

   public Period(Date start, Date end) {
      if(start.after(end))
         throw new IllegalArgumentException("시작 시간이 종료 시간 이후일 수 없다.");

      this.start = start;
      this.end = end;
   }

   public Date getStart() {
      return start;
   }

   public Date getEnd() {
      return end;
   }
   
   // 직렬화 프록시 생성
   private Object writeReplace() {
       return new SerializationProxy(this);
   }
   
   // 역직렬화를 막기 위한 readObject 메소드
   private void readObject(ObjectInputStream stream) throws InvalidObjectException {
       throw new InvalidObjectException("프록시를 사용해야 한다");
   }
   
   // 직렬화 프록시 클래스
   private static class SerialzationProxy implements Serializable {
       private final Date start;
       private final Date end;
       
       SerialzationProxy(Period period) {
           this.start = period.start;
           this.end = period.end;
       }
       
       private Object readResolve() {
           return new Period(start, end);               // 원본 Period 객체 복원
       }
   }
}
```
위 코드에서 `Period`클래스에서 `writeReplace`메소드를 통해 `직렬화 프록시(SerializationProxy)`를 반환한다. <br><br>

직렬화 시 프록시 객체가 사용되며 역직렬화 시, 프록시 객체의 `readResolve` 메소드가 호출되어 원본 `Period` 객체가 생성된다. <br><br>

이 방식은 `역직렬화 중 원본 객체의 불변성을 지켜주고` 잠재적인 보안 문제를 방지할 수 있다. <br><br>

`readObject` 메소드를 사용하지 못하도록 막음으로써 직렬화 프록시를 반드시 사용하게 강제한다. 

<br><br>


### 정리하자면
1. `직렬화 프록시 패턴`을 사용하면 직렬화와 역직렬화 과정에서 발생할 수 있는 여러 가지 문제를 해결할 수 있다. 
2. 이 패턴은 `불변 객체`에 특히 유용하며, 객체의 무결성을 유지하면서 안전하게 직렬화를 수행할 수 있게 한다.
3. `readObject`대신 `writeReplace`와 `readResolve`를 사용하여 객체의 상태가 변질되지 않도록 `방어적 설계`가 가능하다.
4. 복잡한 상속 관계를 가진 객체보다는 주로 `단순하고 불변성`을 가진 객체에서 더 적합하게 사용된다.
