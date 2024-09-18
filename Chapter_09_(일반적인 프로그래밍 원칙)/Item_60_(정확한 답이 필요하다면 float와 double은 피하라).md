# Item 60. 정확한 답이 필요하다면 float나 double은 피하라

### 내용 요약 <br>
1. `float`와 `double`의 문제점
    - `float`와 `double`은 부동소수점 방식을 사용하기 때문에 소수점 이하의 숫자를 정확하게 표현하지 못하는 경우가 있다. <br><br>

    - 이로 인해 정밀도 손실이 발생할 수 있으며, 특히 금융 계산이나 정밀한 과학적 계산에서 부정확한 결과를 초래할 수 있다. <br><br>


2. 금융 계산에 적합한 대안
    - `BigDecimal`클래스는 `float`나 `double`과 달리 무한 소수를 다룰 수 있는 정확한 소수 계산을 지원한다. <br><br>
    - 그러나 `BigDecimal`은 연산이 상대적으로 느리고 코드가 더 복잡해지는 단점이 있다. <br><br>
    - 더 간단한 방법으로, 금액을 센트(소수점 없이 정수 단위)로 변환한 후 `int`나 `long`타입을 사용하는 방식도 있다. <br><br>


3. 언제 `float`와 `double`을 사용해야 하는가
    - `float`와 `double`은 대략적인 계산 or 성능이 중요한 경우에 적합하다. <br>
      예를 들어, 그래픽 계산이나 물리 시뮬레이션에서 빠른 성능이 필요할 때는 부동소수점 타입을 사용할 수 있다. <br><br>


4. 정확한 계산이 필요한 경우의 권장사항
    - 가능한 한 `BigDecimal`을 사용하거나, 필요한 경우 정수 타입(`int`, `long`)을 사용하는 것이 더 안전하다.


<br><br>


### 잘못된 예시 : `float`와 `double`을 사용한 금전 계산
```java
import java.util.*;

public class Item60Example {
    
    public static void main(String[] args) {
        // float와 double을 사용한 금전 계산
        double itemPrice = 1.03;
        double amountPaid = 2.00;
        
        // 잘못된 경과 : 정밀도 손실로 인해 기대한 값이 나오지 않음
        System.out.println("거스름돈 : " + (amountPaid - itemPrice));           // 0.97이 아닌 0.96999999999999999 출력
    }
}
```
위 코드에서 `double`을 사용해 금액을 계산했지만 기대한 결과인 `0.97`대신 `0.96999999999999999`가 출력된다. <br>
이는 부동소수점 방식으로 인한 정밀도 손실 때문이다. 


<br><br>


### 올바른 예시 1 : `BigDecimal`을 사용한 금액 계산
```java
import java.util.*;

public class Item60Example {
    
    public static void main(String[] args) {
        // BigDecimal을 사용한 금전 계산
        BigDecimal itemPrice = new BigDeciaml("1.03");
        BigDecimal amountPaid = new BigDeciaml("2.00");
        
        // 정확한 결과 : 정밀도 손실 없이 정확한 값이 출력됨
        System.out.println("거스름돈 : " + amountPaid.substract(itemPrice));           // 0.97 출력
    }
}
```
위 코드에서 `BigDecimal`을 사용해 금전 계산을 하면 정밀도 손실 없이 정확한 결과가 나온다. <br>
다소 코드가 복잡해질 수 있지만 정확한 계산이 필요할 때 적합하다.


<br><br>


### 올바른 예시 2 : `정수형 타입`을 사용한 금액 계산
```java
import java.util.*;

public class Item60Example {
    
    public static void main(String[] args) {
        // 센트를 기준으로 금액을 정수로 변환하여 계산
        int itemPriceInCents = 103;
        int amountPaidInCents = 200;
        
        // 정수 계산으로 정확한 결과
        System.out.println("거스름돈 : " + (amountPaidInCents - itemPriceInCents) + " cents");           // 97 cents 출력
    }
}
```
위 코드에서 센트 단위로 변환하여 정수형 `int`으로 계산하면 간단하면서도 정확한 결과를 얻을 수 있다. <br>
이게 성능과 정확성 모두를 고려한 방법이다. <br>

