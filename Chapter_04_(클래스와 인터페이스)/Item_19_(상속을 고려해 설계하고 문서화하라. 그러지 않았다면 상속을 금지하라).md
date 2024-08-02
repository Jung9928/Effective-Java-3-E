# Item 19. 상속을 고려해 설계하고 문서화하라. 그렇지 않으면 상속을 금지하라.

### 내용 요약 <br>
1. `상속`을 고려한 설계와 문서화
    - 상속을 염두에 둔 설계를 할 때는 하위 클래스에서 재정의할 수 있는 메소드를 명확히 정의하고 문서화해야 한다.
    - 재정의 할 수 있는 메소드에 대한 명세를 명확히 기술해서 하위 클래스에서 해당 메소드의 명세를 준수할 수 있도록 한다. 
    - 상속을 허용하는 클래스는 안전하게 상속할 수 있도록 설계해야 하며, 상위 클래스의 변경이 하위 클래스에 미치는 영향을 최소화해야 한다. <br><br>

2. `상속`을 금지하는 방법
   - 상속을 금지하려면 클래스를 `final`로 선언하거나 모든 생성자와 정적 팩토리 메소드를 `private` 또는 `private-package`으로 선언한다. <br><br>

3. 문서화
   - 상속을 허용하는 메소드는 하위 클래스에서 재정의할 때 어떻게 동작해야 하는 지 명확히 문서화해야 한다. <br>
   - 재정의 가능한 메소드가 호출하는 모든 메소드를 문서화해야 한다. <br><br>

### `상속`을 고려한 설계
```java
public abstract class AbstractShape {
    private int x;
    private int y;
    
    public AbstractShape(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    public int getX() {
        return x;
    }
    
    public int getY() {
        return y;
    }
    
    public abstract void draw();
    
    public final void moveTo(int newX, int newY) {
        x = newX;
        y = newY;
        draw();
    }
}
```
위 클래스는 하위 클래스가 `draw` 메소드를 반드시 구현하도록 강제한다. `moveTo` 메소드는 `final`로 선언되어 있어 <br>
하위 클래스가 이 메소드를 재정의할 수 없으며, 따라서 핵심적인 이동기능은 변경될 수 없다.

<br>

```java
public final class ImmutablePoint {
    private final int x;
    private final int y;
    
    public ImmutablePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    public int getX() {
        return x;
    }
    
    public int getY() {
        return y;
    }
}
```
이 클래스는 `final`로 선언되어 있어 상속할 수 없다. 또한, 모든 필드가 `final` 이므로 객체가 불변성을 가진다.

<br>

### 정리하자면
- `상속`을 고려한 설계와 문서화는 클래스의 안정성과 유지보수성을 높이므로 가능하다면 설계와 문서화를 수행하자.
