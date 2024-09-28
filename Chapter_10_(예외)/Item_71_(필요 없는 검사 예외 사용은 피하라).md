# Item 71. 필요 없는 검사 예외 사용은 피하라

### 내용 요약 <br>
1. 검사 예외(Checked Exception)의 단점
    - 검사 예외는 `호출자에게 부담`을 줄 수 있다. <br>
      호출자는 항상 예외를 처리하기 위해 `try-catch` 블록을 작성해야 하므로 코드가 복잡해질 수 있다. <br><br>

    - 지나치게 많은 검사 예외를 사용하면, 사용자가 해당 API를 `불편하게` 느낄 수 있으며 이는 API의 `사용성을 저해`할 수 있다. <br><br>

2. 대안 방법
    - `상태 검사 메소드`를 제공하라
      - 검사 예외를 던지기 전에 호출자가 문제를 사전에 확인할 수 있도록 상태를 확인하는 메소드를 제공하는 것이 좋다. <br>
        예를 들어, 파일이 존재하는 지 여부를 검사하는 메소드를 제공하여 호출자가 미리 파일을 확인하고 처리할 수 있게 한다. <br><br>

    - `옵셔널 반환`
      - 메소드가 실패할 가능성이 있는 경우, 검사 예외 대신 `Optional`을 반환하여 호출자가 실패 상황을 명확하게 처리할 수 있도록 한다. <br><br>

    - `런타임 예외 사용 고려`
      - 만약 예외 상황이 복구 불가능한 경우라면, 런타임 예외를 사용하는 것이 더 적절할 수 있다. <br>
        특히, 호출자가 오류를 예측하고 복구할 필요가 없는 경우에는 런타임 예외로 처리하는 것이 좋다. <br><br>

3. 검사 예외가 필요한 경우
    - 검사 예외는 정말로 호출자가 복구할 수 있는 `예외 상황`에서만 사용하는 것이 좋다. <br>
      그렇지 않다면 오히려 코드의 `유연성`을 떨어뜨리고 사용자의 `부담`을 증가시킨다. <br><br>


<br><br>



### 불필요한 검사 예외 사용 예시
```java
public class Account {
    
    private double balance;
    
    public void withdraw(double amount) throws Exception {
        if(amount > balance) {
            throw new Exception("잔액이 부족하다.");
        }
        
        balance -= amount;
    }
}
```
위 코드에선 `Exception`을 던지고 있지만, 실제로 호출자는 잔액 부족 상황을 미리 확인할 수 있다. <br>
예외 처리보다 사전 검사를 통해 더 효율적으로 처리할 수 있다. <br><br>

이 경우, `Exception`을 남용한 예시로, 호출자에게 불필요한 `try-catch`블록을 강제하게 만든다.

<br><br>



### 상태 검사 메소드 사용 예시
```java
public class Account {
    
    private double balance;
    
    public boolean canWithdraw(double amount) {
        return amount <= balance;
    }
    
    public void withdraw(double amount) {
        if(!canWithdraw(amount)) {
            throw new IllegalArgumentException("잔액이 부족하다.");
        }
        
        balance -= amount;
    }
}
```
위 코드에선 예외를 던지기 전에 호출자가 미리 잔액을 확인할 수 있도록 `canWithdraw` 메소드를 제공했다. <br>
호출자는 `canWithdraw`를 통해 잔액이 충분한 지 확인한 후 출금을 진행할 수 있다. <br>
이는 불필요한 예외 처리를 피하면서도 호출자에게 더 나은 `제어권`을 준다. <br><br>

<br><br>




### `Optional`을 사용한 예시
```java
import java.util.Optional;

public class Account {
    
    private double balance;
    
    public Optional<String> withdraw(double amount) {
        if(amount > balance) {
            return Optional.of("잔액이 부족하다.");
        }
        balance -= amount;
        
        return Optional.empty();
    }
}

public class Main {

   public static void main(String[] args) {
      Account account = new Account();
      account.withdraw(100).ifPresent(System.out::println);
   }
}
```
위 코드에선 예외 대신 `Optional`을 반환하여 호출자가 상황에 맞게 처리할 수 있도록 한다. <br>
잔액이 부족할 경우, 메시지를 반환하고 문제가 없으면 `Optional.empty()`를 반환한다. <br><br>

호출자는 반환된 `Optional`객체를 통해 쉽게 예외 상황을 처리할 수 있다. <br>
호출자는 `ifPresent`를 통해 잔액 부족 메시지를 처리할 수 있다. `Optional`을 사용한 방식은 코드의 `가독성`을 높이고 <br>
불필요한 예외처리를 피하면서도 예외적인 상황을 우아하게 처리할 수 있다. <br>

<br><br>


### 정리하자면
1. `필요 없는 검사 예외`는 사용하지 않도록 주의하자. 지나친 예외 사용은 API 사용자르 불편하게 만들고 코드 복잡성만 증가시킨다.
2. 상태를 사전에 확인할 수 있는 메소드를 제공하거나 `Optional`과 같은 대안을 사용하여 예외적인 상황을 더 우아하게 처리하는 방법을 고려해야 한다. 
3. 검사 예외는 호출자가 `복구 가능한 상황`에서만 사용하는 것이 적절하며, 불필요하게 남용하지 않아야 한다.





