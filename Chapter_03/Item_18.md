# Item 18. 상속보다는 컴포지션을 사용하라

### 내용 요약 <br>
1. `상속`의 단점
    - 상위 클래스의 구현에 강하게 결합된다.
    - 상위 클래스의 내부 구현이 변경되면 하위 클래스도 영향을 받는다.
    - 상위 클래스의 새로운 버전이 나왔을 때, 하위 클래스가 제대로 동작하지 않을 수 있다.
    - 상위 클래스의 모든 public/protected 멤버가 하위 클래스에 노출된다.
   
<br>

2. `컴포지션`의 장점
   - 상속의 단점을 피할 수 있다.
   - 구현이 아닌 인터페이스를 사용하여 결합도를 낮출 수 있다.
   - 기존 클래스의 기능을 재사용할 수 있다.
   - 캡슐화를 유지하며 더 유연하고 유지보수하기 쉬운 코드를 작성할 수 있다.

<br>

3. Wrapper 패턴
   - 컴포지션을 구현하는 한 가지 방법이다.
   - 새로운 클래스가 기존 클래스의 인스턴스를 필드로 가지며, 기존 클래스의 메소드를 호출하여 기능을 구현한다.
   - 기존 클래스의 인터페이스를 그대로 노출하거나 새로운 인터페이스를 제공할 수 있다.
   - 상속의 단점을 피하고 더 유연하고 유지보수하기 쉬운 코드를 작성할 수 있다.

<br>

### `상속`을 사용할 때의 단점 예시
```java
import java.util.*;

public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;
    
    public InstrumentedHashSet() {
    }
    
    public InstrumentedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
    }
    
    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }
}
```
위 예시에서 `InstrumentedHashSet` 클래스는 `HashSet`을 상속받아 요소가 추가될 때 마다 `addCount`를 증가시키도록 한다. <br>
그러나 `HashSet`의 `addAll` 메소드는 내부적으로 `add` 메소드를 호출하므로 `addCount`가 2배로 증가하는 문제가 발생할 수 있다. <br><br>


### `컴포지션`을 사용하여 위 코드와 동일한 기능을 구현한 예시
```java
import java.util.*;

public class InstrumentedHashSet<E> extends ForwardingSet<E> {
    private int addCount = 0;
    
    public InstrumentedHashSet(Set<E> s) {
        super(s);
    }
    
    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }
}

class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    
    public ForwardingSet(Set<E> s) {
        this.s = s;
    }
    
    @Override
    public boolean add(E e) {
        return s.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        return s.addAll(c);
    }

   @Override
   public void clear() {
      s.clear();
   }

   @Override
   public boolean contains(Object o) {
      return s.contains(o);
   }

   @Override
   public boolean containsAll(Collection<?> c) {
      return s.containsAll(c);
   }

   @Override
   public boolean isEmpty() {
      return s.isEmpty();
   }

   @Override
   public Iterator<E> iterator() {
      return s.iterator();
   }

   @Override
   public boolean remove(Object o) {
      return s.remove(o);
   }

   @Override
   public boolean removeAll(Collection<?> c) {
      return s.removeAll(c);
   }

   @Override
   public boolean retainAll(Collection<?> c) {
      return s.retainAll(c);
   }

   @Override
   public int size() {
      return s.size();
   }

   @Override
   public Object[] toArray() {
      return s.toArray();
   }

   @Override
   public <T> T[] toArray(T[] a) {
      return s.toArray(a);
   }

   @Override
   public boolean equals(Object o) {
      return s.equals(o);
   }

   @Override
   public int hashCode() {
      return s.hashCode();
   }
}
```
위 예시에서 `InstrumentedSet` 클래스는 `ForwardingSet` 클래스를 상속받아 구현되었다. <br>
`ForwardingSet` 클래스는 `Set` 인터페이스를 구현하며, 실제 작업을 위임한다. <br>
이를 통해, `InstrumentedSet` 클래스는 `HashSet`의 내부 구현에 의존하지 않으며 상속으로 인한 문제를 피랗 수 있다. <br>
