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
