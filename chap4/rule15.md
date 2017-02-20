## 규칙15. 변경 가능성을 최소화하라
변경 불가능(immutable) 클래스는 그 객체를 수정할 수 없는 클래스다.

### 변경 불가능 클래스를 만들 때의 규칙

1. 객체 상태를 변경하는 메서드(수정자 메서드 등)를 제공하지 않는다.
2. 계승할 수 없도록 한다.
  - 보통 클래스를 final로 선언하면 된다.
3. 모든 필드를 final로 선언한다.
4. 모든 필드를 private로 선언한다.
5. 변경 가능 컴포넌트에 대해 독점적 접근권을 보장한다.
  - 클래스에 포함된 변경가능 객체에 대한 참조를 클라이언트는 획득할 수 없어야 한다.
  - 생성자나 접근자, readObject 메서드 안에서는 ___방어적 복사본(defensive copy)___을 만들어야 한다.
  
  
```java
/**
 * @author  Josh Bloch (책 저자 ㅋ)
 */
public class BigInteger extends Number implements Comparable<BigInteger> {
  final int signum;
  final int[] mag;
  
  public BigInteger add(BigInteger val) {
    if (val.signum == 0)
        return this;
    if (signum == 0)
        return val;
    if (val.signum == signum)
        return new BigInteger(add(mag, val.mag), signum);

    int cmp = compareMagnitude(val);
    if (cmp == 0)
        return ZERO;
    int[] resultMag = (cmp > 0 ? subtract(mag, val.mag)
                       : subtract(val.mag, mag));
    resultMag = trustedStripLeadingZeroInts(resultMag);

    return new BigInteger(resultMag, cmp == signum ? 1 : -1);
  }
}
```
자바 플랫폼 라이브러리의 String, 기본 자료형 클래스, BigInteger, BigDecimal 등이 변경 불가능한 클래스이다.<br>
사칙연산 각각은 this 객체를 변경하는 대신 새로운 BigInteger 객체를 만들어 반환하도록 구현되어 있음을 유의하라.<br>
대부분의 변경 불가능 클래스가 따르는 패턴이고 ___함수형 접근법(functional approach)___으로도 알려져있다.

### 변경 불가능 클래스의 장점

#### 1. 변경 불가능 객체는 단순하다.

생성될 때 부여된 한 가지 상태만 가지므로 생성자가 불변식(invariant)을 확실히 따른다면, 해당 객체는 불변식을 절대로 어기지 않게 된다.

#### 2. 변경 불가능 객체는 스레드에 안전(thread-safe)하다.

어떤 동기화도 필요 없으며, 여러 스레드가 동시에 사용해도 상태가 훼손될 일이 없다.

#### 3. 변경 불가능 객체는 자유롭게 공유할 수 있다.
변경 불가능 클래스는 클라이언트가 기존 객체를 재사용하도록 적극 장려해서 이런 장점을 충분히 살릴 필요가 있다.

```java
public static final Complex ZERO = new Complex(0, 0);
public static final Complex ONE = new Complex(1, 0);
```

자주 사용되는 값을 public static final 상수로 만들어 제공하는 것이다.

```java
public class BigInteger extends Number implements Comparable<BigInteger> {
	/**
	 * The cache of powers of each radix.  This allows us to not have to
	 * recalculate powers of radix^(2^n) more than once.  This speeds
	 * Schoenhage recursive base conversion significantly.
	 */
	private static volatile BigInteger[][] powerCache;
    
	static {
		for (int i = 1; i <= MAX_CONSTANT; i++) {
		    int[] magnitude = new int[1];
		    magnitude[0] = i;
		    posConst[i] = new BigInteger(magnitude,  1);
		    negConst[i] = new BigInteger(magnitude, -1);
		}
	
	    /*
	     * Initialize the cache of radix^(2^x) values used for base conversion
	     * with just the very first value.  Additional values will be created
	     * on demand.
	     */
	    powerCache = new BigInteger[Character.MAX_RADIX+1][];
	    logCache = new double[Character.MAX_RADIX+1];
	
	    for (int i=Character.MIN_RADIX; i <= Character.MAX_RADIX; i++) {
	        powerCache[i] = new BigInteger[] { BigInteger.valueOf(i) };
	        logCache[i] = Math.log(i);
	    }
	}
}
```

더 발전하면 변경 불가능 클래스는 자주 사용하는 객체를 캐시하여 이미 잇는 객체가 거듭 생성되지 않도록 하는 정적 팩터리를 제공할 수 있다. 기본 자료형에 대한 객체 클래스들(boxed primitive class)과 BigInteger 클래스는 그렇게 구현되어 있다.

#### 4. 변경 불가능한 객체는 그 내부도 공유할 수 있다.

```java
/**
 * This internal constructor differs from its public cousin
 * with the arguments reversed in two ways: it assumes that its
 * arguments are correct, and it doesn't copy the magnitude array.
 */
BigInteger(int[] magnitude, int signum) {
    this.signum = (magnitude.length == 0 ? 0 : signum);
    this.mag = magnitude;
    if (mag.length >= MAX_MAG_LENGTH) {
        checkRange();
    }
}

/**
 * Returns a BigInteger whose value is {@code (-this)}.
 *
 * @return {@code -this}
 */
public BigInteger negate() {
    return new BigInteger(this.mag, -this.signum);
}
```

negate 메서드는 같은 크기의 값을 부호만 바꿔서 새로운 BigInteger 객체로 반환한다.
그러나 배열을 복사하지 않고 원래 객체와 같은 내부 배열을 참조한다.

#### 5. 변경 불가능 객체는 다른 객체의 구성요소로도 훌륭하다.
변경 불가능 객체는 맵의 키나 집합의 원소로 활용하기 좋다. 한번 집어놓고 나면 그 값이 변경되어 맵이나 집합의 불변식이 깨질 걱정은 하지 않다도 된다.

### 변경 불가능 객체의 유일한 단점

#### 1. 값마다 별도의 객체를 만들어야 한다.
객체 생성 비용이 높을 가능성이 크다. 큰 객체라면 특히 더 그렇다.

```java
BigInteger moby = ...;
moby = moby.flipBit(0);
```

moby가 백만 비트라면 원래 값과 딱 한 비트만 바꾸고 싶어도 새로운 BigInteger 객체를 생성해야된다.<br>
단계별로 새로운 객체를 만들고 결국에는 마지막 객체를 제외한 모든 객체를 버리는 연산을 수행해야 하는 경우 성능 문제는 더 커진다.

### 단점의 대응 방법

#### 1. 다단계 연산 가운데 자주 요구되는 것을 기본 연산(primitive)으로 제공하는 것이다.

```java
/*
 * The Java language provides special support for the string
 * concatenation operator (&nbsp;+&nbsp;), and for conversion of
 * other objects to strings. String concatenation is implemented
 * through the {@code StringBuilder}(or {@code StringBuffer})
 * class and its {@code append} method.
 */
public final class String {
	...
}
```

#### 2. 변경 가능한 package-private 동료 클래스를 사용한다.

클라이언트가 변경 불가능 클래스에 어떤 다단계 연산을 적용할지 정확하게 예측할 수 있을 때 쓸 수 있다.<br>
자바 플랫폼 라이브러리에 있는 좋은 사례는 String 클래스다. 이 클래스의 변경 가능 동료 클래소로는 StringBuilder가 있다.


### 요약
- 구현 규칙은, 어떤 메서드도 객체를 수정해서는 안 되며, 모든 필드는 final로 선언되어야 한다.(성능 향상을 위해서 완화할 수도 있다.)
- 변경 가능한 클래스로 만들 타당한 이유가 없다면, 반드시 변경 불가능 클래스로 만들어야 한다.
- 변경 불가능한 클래스로 만들 수 없다면, 변경 가능성을 최대한 제한하라.
- 특별한 이유가 없다면 모든 필드를 final로 선언하라.
