# Item 76. 가능한 한 실패 원자적으로 만들라

### 내용 요약 <br>
1. 실패 원자성(Failure Atomicity)
    - 메소드가 예외를 던질 때, `객체의 상태가 변경되지 않도록` 하는 것을 의미한다. <br>
      즉, 메소드가 실패해도 객체는 `일관된 상태`를 유지해야 한다. <br><br>

    - 이는 시스템의 복구 및 유지보수를 쉽게 하고, 예외 처리 로직을 단순화 시킨다. <br><br>

2. `실패 원자성을 보장하는 방법`
    - 불변 객체 사용
      - 불변 객체는 상태를 변경할 수 없으므로 실패 원자성을 자연스럽게 보장한다. <br><br>

    - 작업을 수행하기 전에 유효성 검증
      - 메소드가 작업을 수행하기 전에 필요한 모든 조건을 먼저 검증하여, 실패 가능성을 줄인다. <br><br>

    - 객체의 복사본을 사용한 후 교체
      - 작업을 객체의 복사본ㅇ네서 먼저 수행하고, 모든 작업이 성공적으로 완료되었을 때만 원본 객체의 상태를 변경한다. <br><br>
    
    - 연산을 트랜잭션으로 처리
      - 여러 단계의 작업을 하나의 트랜잭션으로 묶어, 중간에 실패해도 이전 상태로 되돌릴 수 있게 한다. <br><br>


3. 실패 원자성을 달성하기 위한 추가적인 기술
    - 임시 변수 사용
      - 작업을 객체의 상태에 즉시 반영하는 대신, 임시 변수에서 작업을 완료한 후 객체 상태를 변경한다. <br><br>

    - 상태 복구 코드 작성
      - 실패 시 원래 상태로 복구하는 코드 또는 롤백 코드를 명시적으로 작성하여 상태의 일관성을 보장한다. <br><br>


<br><br>


### 실패 원자성을 보장하지 않는 코드
```java
public class Account {

    private int balance;
    
    public Account(int balance) {
        this.balance = balance;
    }
    
    public void withdraw(int amount) {
        if(amount > balance) {
            throw new IllegalArgumentException("잔액이 부족하다.");
        }
        // 여기서 예외가 발생하면 상태가 불일치하게 될 수 있음
        balance -= amount;
    }
    
    public int getBalance() {
        return balance;
    }
}
```
위 코드에선 `withdraw` 메소드는 잔액보다 큰 금액을 인출하려고 할 때 예외를 던지지만 <br>
다른 논리적인 오류로 인해 balance가 예상하지 못한 상태로 변경될 수 있다. <br><br>

예외가 발생하면 메소드가 중단될 수 있고, 객체의 상태는 일관성을 잃을 수 있다. <br><br>

<br><br>


### 실패 원자성을 보장한 개선된 코드
```java
public class Account {

    private int balance;
    
    public Account(int balance) {
        this.balance = balance;
    }
    
    public void withdraw(int amount) {
        if(amount > balance) {
            throw new IllegalArgumentException("잔액이 부족하다.");
        }
        // 임시 변수를 사용하여 실패 원자성 보장
        int newBalance = balance - amount;
        balance = newBalance;
    }
    
    public int getBalance() {
        return balance;
    }
}
```
위 코드에선 `withdraw` 메소드는 `임시변수(newBalance)`를 사용하여 연산을 먼저 수행하고 <br>
성공적인 경웅에만 실제 상태를 업데이트 한다. <br><br>

이 방식은 메소드가 실패하더라도 객체의 상태가 `변경되지 않고`, 실패 원자성을 보장한다. <br><br>

<br><br>




### 불변 객체를 활용한 예시
```java
public final class Complex {

    private final double real;
    private final double imaginary;
    
    public Complex(double real, double imaginary) {
        this.real = real;
        this.imaginary = imaginary;
    }
    
    public Complex add(Complex other) {
        return new Complex(this.real + other.real, this.imaginary + other.imaginary);
    }
}
```
위 코드에선 불변 객체인 `Complex`는 실패 원자성을 자연스럽게 보장한다. <br>
모든 연산은 새로운 객체를 반환하며, 원래 객체의 상태는 절대 변경되지 않는다. <br><br>

불변 객체를 사용하면, 작업이 실패하더라도 기존 객체는 전혀 영향을 받지 않는다. <br><br>

<br><br>


### 정리하자면
1. 메소드가 실패해도 `객체 상태의 일관성`을 유지하는 것은 중요한 설계 원칙이다. <br>
   이를 위해, `실패 원자성`을 보장하는 방법을 사용해야 한다. <br><br>

2. 불변 객체를 사용하거나, 연산을 수행하기 전에 미리 검증하고 임시 변수를 활용해 작업을 완료한 후 상태를 변경하는 방식 등을 통해 실패 원자성을 달성할 수 있다. <br><br>

3. 이러한 설계를 통해 예외가 발생해도 시스템이 예측 가능한 상태를 유지할 수 있고 복구 및 디버깅이 용이해진다. 