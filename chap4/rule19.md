## 규칙19. 인터페이스는 자료형을 정의할 때만 사용하라

인터페이스를 구현하는 클래스를 만들게 되면, 그 인터페이스는 해당 클래스의 객체를 참조할 수 있는 자료형(type) 역할을 하게 된다. 그 외 다른 목적으로 인터페이스를 정의하고 사용하는 것은 적절치 못하다.

이 기준에 미달하는 사례로 소위 상수 인터페이스(constant interface)라는 것이 있다.

```java
public interface ObjectStreamConstants {
    short STREAM_MAGIC = -21267;
    short STREAM_VERSION = 5;
    byte TC_BASE = 112;
    byte TC_NULL = 112;
    ...
}
```

자바 플랫폼 라이브러리의 java.io. ObjectStreamConstants 같은 것이다. 상수 인터페이스 패턴은 인터페이스를 잘못 사용한 것이다.

다음번 릴리스에 더 이상 그런 상수를 사용하지 않도록 클래스를 변경할 것이라 가정하면, 이진 호환성(binary compatibility)을 보장하려면 그 인터페이스를 계속 구현해야 한다.

상수를 API 일부로 공개하고 싶을 때는, 해당 상수가 이미 존재하는 클래스나 인터페이스에 강하게 연결되어 있을 때는 그 상수들을 해당 클래스나 인터페이스에 추가해야 한다.

```java
public final class Integer extends Number implements Comparable<Integer> {
    /**
     * A constant holding the minimum value an {@code int} can
     * have, -2<sup>31</sup>.
     */
    @Native public static final int   MIN_VALUE = 0x80000000;

    /**
     * A constant holding the maximum value an {@code int} can
     * have, 2<sup>31</sup>-1.
     */
    @Native public static final int   MAX_VALUE = 0x7fffffff;
    
    ...
}
```

기본 자료형의 객체 표현형들(Integer나 Double)에는 MIN_VALUE와 MAX_VALUE 상수가 공개되어 있다.

이런 상수들이 enum 자료형의 멤버가 되어야 바람직할 때는 enum 자료형(규칙 30)과 함께 공개해야 한다. 그렇지 않을 때는 해당 상수들을 객체 생성이 불가능한 유틸리티 클래스(규칙 4)에 넣어서 공개해야 한다.

```java
package com.test.java.science;

public class PysicalConstants {
	private PysicalConstants() {}
	
	public static final double AVOGADROS_NUMBER = 6.1231432423e23;
	public static final double BOLTZMANN_CONSTANT = 1.2342352e-13;
}
```

유틸리니 클래스에 포함된 상수를 이용할 일이 많다면, JDK 1.5부터 도입된 정적 임포트(static import) 기능을 사용하면 클래스 이름을 제거할 수 있다.

```java
import static com.test.java.science.PysicalConstants.*;

public class Test {
	double atoms(double mols) {
		return AVOGADROS_NUMBER * mols;
	}
}
```

### 요약
인터페이스는 자료형을 정의할 때만 사용해야 한다. 특정 상수를 API의 일부로 공개할 목적으로는 적절치 않다.
