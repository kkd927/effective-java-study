## 규칙18. 추상 클래스 대신 인터페이스를 사용하라
자바 언어에는 여러 가지 구현을 허용하는 자료형을 만드는 방법이 두 가지 포함되어 있다. 인터페이스와 추상 클래스(abstract class)이다.

인터페이스는 인터페이스가 규정하는 일반 규약(general contract)을 지키기만 하면되며, 그렇게 만든 클래스는 클래스 계층(class hierarchy)에 속할 필요가 없다.

추상 클래스는 추상 클래스가 규정하는 자료형을 구현하기 위해서 반드시 계승을 해야 한다. 이에 따른 많은 제약이 발생하게 된다.

### 1. 이미 있는 클래스를 개조해서 새로운 인터페이스를 구현하도록 하는 것은 간단하다

필요한 메서드가 다 있는지 확인해서 없으면 추가한 다음 클래스 선언부에 implements 절을 넣는 것이 전부다.

### 2. 인터페이스는 믹스인(mixin)을 정의하는데 이상적이다

믹스인은 클래스가 "주 자료형(primary type)" 이외에 추가로 구현할 수 있는 자료형으로, 어떤 선택적 기능을 제공한다는 사실을 선언하기 위해 쓰인다. (예. Comparable 인터페이스) 

추상 클래스는 상위 클래스가 하나뿐이며, 클래스 계층에는 믹스인을 넣기 좋은 곳은 없다.

### 3. 인터페이스는 비 계층적인(nonhierarchical) 자료형 프레임워크(type framework)를 만들 수 있도록 한다

어떤 것들은 자료형 계층(type hierarchy)으로 잘 정리되지만, 계층 안에 잘 들어 맞지 않는 것들도 있다.

가수와 작곡가를 표현하는 인터페이스를 예를 들면,

```java
public interface Singer {
	AudioClip sing(Song s);
}

public interface Songwriter {
	Song compose(boolean hit);
}
```

가수이자 작곡가인 경우 문법적 문제 없이 Singer와 Songwriter를 동시에 구현하는 클래스를 만들 수 있다.

```
public interface SingerSongwriter extends Singer, Songwriter {
	AudioClip strum();
	void actSensitive();
}
```

추상 클래스의 경우 필요한 조합마다 별도의 클래스를 만들어야하는 조합 폭증(combinatorial explosion) 문제를  겪을 수 있다.

### 4. 인터페이스를 사용하면 포장 클래스 숙어(wrapper class idiom)을 통해 안전하면서도 강력한 기능 개선이 가능하다 ([규칙16](../rule16.md))

### 5. 추상 골격 구현(abstract skeletal implementation) 클래스를 중요 인터페이스마다 두면, 인터페이스의 장점과 추상 클래스의 장점을 결합할 수 있다

인터페이스로는 자료형을 정의하고, 구현하는 일은 골격 구현 클래스에 맡기면 된다.

관습적으로 골격 구현 클래스의 이름은 Abstract*Interface*와 같이 정한다. 예를들어 컬렉션 프레임워크(Collection Framework)에는 인터페이스별로 골격 구현 클래스들이 하나씩 제공된다. AbstractCollection, AbstractSet, AbstractList, AbstractMap 등이다.

```java
// 골격 구현 위에서 만들어진 완전한 List 구현
static List<Integer> intArrayAsList(final int[] a) {
	if (a == null) {
		throw new NullPointerException();
	}
	
	return new AbstractList<Integer>() {
		public Integer get(int i) {
			return a[i];
		}
		
		@Override public Integer set(int i, Integer val) {			int oldVal = a[i];
			a[i] = val;
			return oldVal;
		}
		
		public int size() {
			return a.length;
		}
	}
}
```

골격 구현 클래스를 적절히 정의하기만 하면, 프로그래머는 쉽게 인터페이스를 구현할 수 있다. 위의 정적 팩터리 메서드는 기능적으로 완전한 List를 구현한다.

추상 클래스를 자료형 정의 수간으로 사용했을 때 만족해야 하는 심각한 제약사항들을 따르지 않아도 추상 클래스를 구현할 수 있도록 돕는다는 것이 골격 구현 클래스의 아름다움이다.

골격 구현 클래스를 상속하도록 기존 클래스를 변경할 수 없다면, 인터페이스를 직접 구현해도 된다. 이럴 경우에도 골격 구현 클래스를 계승하는 private 내부 클래스(inner class)를 정의하고, 인터페이스 메서드에 대한 호출은 해당 중첩 클래스 객체로 전달(forwarding)하면 더 쉽게 구현할 수 있다. 이런 기법은 모의 가상 상속(simulated mutiple inheritance)라고 알려져있다.

골격 구현 클래스를 만들 때는 다른 메서드를 구현할 때 쓸 기본 메서드(primitive method)들을 추려낸다. 이 기본 메서드들은 골격 구현 클래스에 추상 메서드로 선언하고, 나머지 메서드들은 실제로 구현해서 넣는다. 아래는 그 예이다.

```java
// 골격 구현
public abstract class AbstractMapEntry<K,V>
			implements Map.Entry<K,V> {
	
	// 기본 메서드(primitive method)
	public abstract K getKey();
	public abstract V getValue();
	
	// 변경 가능 맵에 들어갈 Entry는 이 메서드를 재정의해야 한다.
	public V setValue(V value) {
		throw new UnsupportedOperationException();
	}
	
	// 일반 규약 구현
	@Override public boolean equals(Object o) {
		...
	}
	
	@Override public int hashCode() {
		...
	}
}
```

골격 구현 클래스는 계승 용도로 설계하는 클래스이므로, [규칙 17](../rule17.md)의 모든 지침을 따라야 한다.

골격 구현의 작은 변종 가운데 하나로는 간단 구현(simple implementation)이라는 것이 있으며, 그 사례는 AbstractMap.SimpleEntry에서 찾아볼 수 있다. 추상 클래스가 아니고 실제 동작하는 구현체 가운데 가장 간단한 형태이다.

### 6. 인터페이스보다는 추상 클래스가 발전시키기 쉽다

다양한 구현을 허용하는 자료형을 추상 클래스로 정의하면 인터페이스보다 나은 점이 한가지 있는데, 인터페이스보다 추상 클래스가 발전시키기 쉽다는 것이다.

일반적으로 보자면, public 인터페이스를 구현하는 기존 클래스를 깨뜨리지 않고 새로운 메서드를 인터페이스에 추가할 방법은 없다. (자바 1.8 부터는 __default__ 메서드를 통해 기존 클래스를 깨뜨리지 않고 새 메서드를 추가할 수 있다)

인터페이스가 공개되고 널리 구현된 다음에는, 인터페이스 수정이 거의 불가능하기 때문에 public 인터페이스는 신중하게 설계해야 한다.

### 요약
- 인터페이스는 다양한 구현이 가능한 자료형을 정의하는 일반적으로 가장 좋은 방법
- 유연하고 강력한 API를 만드는 것보다 개선이 쉬운 API를 만드는 것이 중요한 경우에는 예외<br>
(그런 경우 추상 클래스를 사용해야 한다. 단점을 잘 이해하고 있어야 하며, 그 단점을 수용할 수 있는 경우로 한정해서 사용)
- 중요한 인터페이스를 API에 포함시키는 경우, 골격 구현 클래스를 함께 제공하면 어떨지 고려
- public 인터페이스는 극도로 주의해서 설계해야 하며, 실제로 여러 구현을 만들어보며 광범위 테스트를 해야 한다
