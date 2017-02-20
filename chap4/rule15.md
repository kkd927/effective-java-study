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
대부분의 변경 불가능 클래스가 따르는 패턴이고 함수형 접근법(functional approach)으로도 알려져있다.
