# Item 17. 불변 객체로 만들어라

### 내용 요약 <br>
1. `불변 객체`의 장점
    - `thread-safe`를 보장하며 동기화가 필요 없다.
    - 단순하고 직관적이다.
    - 자유롭게 공유할 수 있다.
    - 다른 객체의 구성 요소로 사용하기 좋다.
    - 불변객체는 상태가 변하지 않으므로 어플리케이션 상태가 부분적으로 변경되지 않는다. 
      <br> 즉, 불변 객체의 메소드 에러가 발생해도 객체의 상태를 변경하지 않는다.
   
<br>

2. `불변 객체`의 설계
   - 객체의 상태를 변경하는 메소드를 제공하지 않는다.
   - 클래스를 확장할 수 없도록 한다.
   - 모든 필드를 `final`로 선언한다.
   - 모든 필드를 `private`으로 선언한다.
   - 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.

<br>

3. `불변 객체`의 사용
   - 객체가 필요할 때 새로운 객체를 생성한다.
   - 복잡한 객체 생성에는 빌더 패턴을 사용한다.
   - 자주 변경되는 객체에는 성능 상의 문제가 발생할 수 있다.

<br>


### `불변 객체`의 예시
```java
public final class Complex {
    private final double re;
    private final double im;
    
    public Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }
    
    public double getReal() {
        return re;
    }
    
    public double getImaginary() {
        return im;
    }
    
    public Complex add(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }
    
    public Complex subtract(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }
    
    public Complex multiply(Complex c) {
        return new Complex(re * c.re - im * c.im, re * c.im + im * c.re);
    }
    
    public Complex divide(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp, (im * c.re - re * c.im) / tmp);
    }
    
    @Override
    public boolean equals(Object o) {
        if(this == o) return true;
        
        if(o == null || getClass() != o.getClass()) 
            return false;
        
        Complex complex = (Complex) o;
        
        return Double.compare(complex.re, re) == 0 && 
                Double.compare(complex.im, im) == 0;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(re, im);
    }
    
    @Override
    public String toString() {
        return "(" + re + " + " + im + "i)";
    }
}
```

### 정리하자면
1. 불변객체는 상태 변경이 불가능한 객체이므로, 스레드 안정성, 단순성, 직관성 등의 장점을 제공한다.
2. 불변객체를 설계할 땐 상태를 변경하는 메소드를 제공하지 않고 클래스를 `final`로 선언하여 모든 필드를 `private`과 `final`로 선언해야 한다
3. 불변객체는 새로운 객체를 생성하여 상태 변경을 처리하며, 복잡한 객체 생성에는 빌더 패턴을 사용할 수 있다.
4. 불변객체는 자주 변경되는 상태를 다루는 경우, 성능 상의 문제가 발생할 수 있으므로 주의가 필요하다.

※ 불변객체를 사용하면 코드의 안정성과 가독성이 크게 향상되며, 특히 멀티스레드 환경에서 안전하게 사용할 수 있다.


