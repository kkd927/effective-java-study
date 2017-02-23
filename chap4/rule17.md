## 규칙17. 계승을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 계승을 금지하라
규칙 16은 계승을 위한 설계와 문서를 갖추지 않은 "이질적(foreign)" 클래스의 하위 클래스를 만들 때 생기는 문제점을 설명하고 있다.

### 재정의 가능 메서드를 내부적으로 어떻게 사용하지는지(self-use) 반드시 문서에 남겨야 한다.

(재정의 가능하다는 것은 public 또는 protected로 선언된 비-final 메서드라는 뜻이다.) 관습적으로, 재정의 가능 메서드를 어떤 식으로 호출하는지는 메서드 주석문 마지막에 명시한다. 주석은 "이 구현은"이라는 문구로 시작한다.

```java
/**
 * {@inheritDoc}
 *
 * <p>This implementation iterates over the collection looking for the
 * specified element.  If it finds the element, it removes the element
 * from the collection using the iterator's remove method.
 *
 * <p>Note that this implementation throws an
 * <tt>UnsupportedOperationException</tt> if the iterator returned by this
 * collection's iterator method does not implement the <tt>remove</tt>
 * method and this collection contains the specified object.
 *
 * @throws UnsupportedOperationException {@inheritDoc}
 * @throws ClassCastException            {@inheritDoc}
 * @throws NullPointerException          {@inheritDoc}
 */
public boolean remove(Object o)
```

이 문서를 보면 iterator 메서드를 재정의하면 remove가 영향 받는다는 사실을 확실히 알 수 있다.

### 클래스 내부 동작에 개입할 수 있는 훅(hooks)을 신중하게 고른 protected 메서드 형태로 제공해야 한다.

protected 멤버 개수는 가능한 한 줄여야 하는데, 구현 세부사항에 대한 일종의 서약(commitment) 구실을 하기 때문이다. 한편으로는 protected 멤버가 너무 적지 않은지도 주의해야 하는데, 잘못하면 계승해서 쓰기엔 형편없는 클래스가 될 수 있기 때문이다.

### 계승을 위해 설계한 클래스를 테스트할 유일한 방법은 하위 클래스를 직접 만들어 보는 것이다.

널리 사용될 클래스를 계승에 맞게 설계할 때는, 문서에 명시한 내부 호출 패턴(self-use pattern)뿐 아니라 메서드와 필드를 protected로 선언하는 과정에 함축된 구현 관련 결정들을 영원히 고수해야 한다는 점을 기억해야 한다. 따라서 다음 릴리스에 성능이나 기능 개선하기 어려워진다. 그런 클래스는 릴리스에 포함시키지 전에 반드시 하위 클래스를 만들어서 테스트해야 한다.

### 생성자는 재정의 가능 메서드를 호출해서는 안된다.

생성자는 직접적이건 간접적이건 재정의 가능 메서드를 호출해서는 안된다. 상위 클래스 생성자는 하위 클래스 생성자보다 먼저 실행되므로, 하위 클래스에서 재정의한 메서드는 하위 클래스 생성자가 실행되기 전에 호출될 것이다.

Cloneable과 Serializable 인터페이스를 사용할 경우에는 계승용 클래스를 설계하기가 특히 더 까다롭다. 계승을 위해 설계하는 클래스에서 Cloneable이나 Serializeable을 구현하기로 결정했다면 비슷한 규칙을 따라야 한다.

clone이나 readObject 메스드 안에서 직접적이건 간접적이건 재정의 가능한 메서드를 호출되지 않도록 주의해야 한다.

### 정리

계승을 위해 클래스를 설계하면 클래스에 상당한 제약이 가해진다. 인터페이스에 대한 골격 구현(skeletal implementation) 추상 클래스(abstract class)를 만드는 경우라면 올바른 결정일 것이다.

계승에 맞도록 설계하고 문서화하지 않은 클래스에 대한 하위 클래스는 만들지 않는 것이다. 가장 쉬운 방법은 클래스를 final로 선언하는 것이다.

재정의 가능 메서드를 내부적으로 사용하는 코드는, 동작 원리를 바꾸지 않고도 기계적으로 제거할 수 있다. 재정의 가능 메서드의 내부 코드를 private로 선언된 "도움 메서드(helper method)" 안으로 옮기고, 각각의 재정의 가능 메서드가 해당 메서드를 호출하게 해야한다.
