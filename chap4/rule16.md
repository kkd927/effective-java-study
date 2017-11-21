## 규칙16. 계승하는 대신 구성하라

계승은 코드 재사용을 돕는 강력한 도구지만, 항상 최선이라고는 할 수 없다. 계승은 상위 클래스와 하위 클래스 구현을 같은 프로그래머가 통제하는 단일 패키지 안에서 사용하면 안전하다. 계승을 고려해 설계되고 그에 맞는 문서를 갖춘 클래스에 사용하는 것도 안전하다(규칙 17). (이번 규칙에서 다루는 계승은 어떤 클래스가 다른 인터페이스를 'implements' 하거나, 어떤 인터페이스가 다른 인터페이스를 'extends'하는 경우에는 적용되지 않는다.)

### 메서드 호출과 달리, 계승은 캡슐화(encapsulation) 원칙을 위반한다.

상위 클래스의 구현은 릴리즈(release)가 거듭되면서 바뀔 수 있는데, 그러다 보면 하위 클래스 코드는 수정된 적이 없어도 망가질 수 있다. 상위 클래스 작성자가 계승을 고려해 클래스를 설계하고 문서까지 만들어 놓지 않았다면, 하위 클래스는 상위 클래스의 변화에 발맞춰 진화해야 한다.

```java
// 계승을 잘못 사용한 사례
public class InstrumentedHashSet<E> extends HashSet<E> {
   // 요소를 삽입하려 한 횟수
   private int addCount = 0;

   public InstrumentedHashSet() {}

   public InstrumentedHashSet(int initCap, float loadFactor) {
       super(initCap, loadFactor);
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

이 코드에서 addAll(Arrays.asList(1, 2, 3)) 하면 addCount는 3이 될 것 같지만 실제로는 6이 된다. 그 이유는 addAll은 HashSet에서 add를 사용하고 있기 때문이다. (HashSet 문서에는 명시 되어있지 않음)


### 대안, 구성(composition) 기법

기존 클래스를 계승하는 대신, 새로운 클래스에 기존 클래스 객체를 참조하는 private 필드를 하나 두는 것이다. 이런 설계 기법을 구성(composition)이라고 부르는데, 새로운 클래스에 포함된 각각의 메서드는 기존 클래스에 있는 메서드 가운데 필요한 것을 호출해서 그 결과를 반환하면 된다. 이런 구현 기법을 전달(forwarding)이라고 하고, 전달 기법을 사용해 구현된 메서드를 전달 메서드(forwarding method)라고 부른다.

```java
 // 계승 대신 구성을 사용하는 포장(wrapper) 클래스
 class InstrumentedSet<E> extends ForwardingSet<E> {
  private int addCount = 0;

  public InstrumentedSet(Set<E> s) {
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

// 재사용 가능한 전달(forwarding)(위임) 클래스
class ForwardingSet<E> implements Set<E> {
  private final Set<E> s;

  public ForwardingSet(Set<E> s) {
    this.s = s;
  }

  public void clear() { 
    s.clear(); 
  }
  
  public boolean contains(Object o) {
    return s.contains(o);
  }
  
  public boolean isEmpty() {
    return s.isEmpty();
  }
  
  public int size() {
    return s.size();
  }
  
  public Iterator<E> iterator() {
    return s.iterator();
  }
  ...
}
```

InstrumentedSet과 같은 클래스를 포장 클래스(wrapper class)라고 부르는데, 다른 Set 객체를 포장하고(감싸고) 있기 때문이다. 이런 구현 기법은 장식자(decorator) 패턴이라 부른다. 때로는 구성과 전달 기법을 아울러서 막연하게 위임(delegation)이라고 부르기도 한다.<br>
포장 클래스는 단점이 별로 없으나, 역호출(callback) 프레임워크와 함께 사용하기에는 적합하지 않다.

전달 메서드 호출 과정에서 성능이 저하되거나, 포장 객체 때문에 메모리 요규량이 늘어나지 않을까 걱정을 할 수도 있지만, 실제로는 큰 영향이 없는 것으로 판명되었다. 전달 클래스는 인터페이스별로 한 번씩만 구현하면 되고, 인터페이스와 같은 패키지에 이미 들어 있는 경우도 있다.

구성 기법이 적절한 곳에 계승을 사용하면 구현 세부사항이 쓸데없이 노출된다. 가장 심각한 문제는 클라이언트가 상위 클래스 상태를 직접 변경하여 하위 클래스의 불변식을 깰 수 있다는 것이다.

계승 메커니즘은 상위 클래스의 문제를 하위 클래스에 전파시킨다. 반면 구성 기법은 그런 약점을 감추는 새로운 API를 설계할 수 있도록 해준다.

### 요약

- 계승은 강력한 도구이지만 캡슐화 원칙을 침해하므로 문제를 발생시킬 소지가 있다
- 상위 클래스와 하위 클래스 사이에 IS-A 관계가 있을 때만 사용하는 것이 좋다
- 설사 IS-A 관계가 성립해도, 하위 클래스가 상위 클래스와 다른 패키지에 있거나 계승을 고려해 만들어진 상위클래스가 아니라면, 하위 클래스는 깨지기 쉽다
- 이런 문제를 피하려면 구성과 전달 기법을 사용하는 것이 좋다
- 포장 클래스 구현에 적당한 인터페이스가 있다면 더욱 구성 기법 사용이 좋다
- 포장 클래스는 하위 클래스보다 견고할 뿐 아니라, 더 강력하다
