## 규칙30. int 상수 대신 enum을 사용하라
열거 자료형(enumerated type)은 고정 개수의 상수들로 값이 구성되는 자료형이다. 자바에 enum이라는 새로운 자료형이 도입되기 전에는 통상 int 형의 상수들을 정의해서 열거 자료형을 흉내 냈다.

```java
// int를 사용한 enum 패턴 - 지극히 불만족 스럽다
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

위의 int enum 패턴은 형 안정성 관점에서도 그렇고, 편의성 관젬에서도 단점이 많다. 오렌지를 기대하는 메서드에 사과 인자로 넘겨도 컴파일러는 불평하지 않는다.

```java
// 귤 맛나는 사과 주스!
int i = (APPLE_FUJI - ORANGE_TEMPLE) / APPLE_PIPPIN;
```

int 대신 String 상수를 사용하는 String enum 패턴이 존재하지만, 더 나쁜 패턴이다. 자바 1.5부터는 int와 String enum 패턴의 문제점을 해소하는 대안이 도입되었다. 바로 enum 자료형이다.

```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
public enum Orange { NAVEL, TEMPLE, BLOOD }
```

자바의 enum 자료형은 완전한 기능을 갖춘 클래스로, 다른 언어의 enum보다 강력하다. 

- 열거 상수(enumeration constant)별로 하나의 객체를 public static final 필드 형태로 제공한다.
- 싱글턴 패턴을 일반화한 것으로(규칙3), 싱글턴 패턴은 본질적으로 보면 열거 상수가 하나뿐인 enum과 같다. 
- 형안전 enum 패턴(typesafe enum pattern)[Bloch01, 규칙21]을 자바 문접에 포함시킨 것이다.
- 컴파일 시점 형 안전성(compile-time type safety)을 제공한다. 
- 같은 이름의 상수가 평화롭게 공존할 수 있도록 한다.
- 임의의 메서드나 필드도 추가할 수 있도록 한다. 임의의 인터페이스를 구현할 수도 있다.
- Comparable 인터페이스(규칙12)와 Serializable 인터페이스가(11장)구현되어 있다.

enum 자료형은 enum 상수 묶음에서 출발해서 점차로 완전한 기능을 갖춘 추상화 단위(abstraction)로 진화해 나갈 수 있다.

```java
// 데이터와 연산을 구비한 enum 자료형
public enum Planet {
	MERCURY (3.302e+23, 2.439e6),
	VENUS   (4.234e+13, 6.042e5),
	EARTH   (5.923e+25, 6.371e5),
	...
	
	private final double mass;
	private final double radius;
	private final double surfaceGravity;
	
	// 중력 상수
	private static final double G = 6.67300E-11;
	
	// 생성자
	Planet(double mass, double radius) {
		this.mass = mass;
		this.radius = radius;
		surfaceGravity = G * mass / (radius * radius);
	}
	
	public double mass() { return mass; }
	public double raidus() { return radius; }
	public double surfaceGravity { return surfaceGravity; }
	
	public double surfaceWeight(double mass) {
		...
	}
}
```

enum 상수에 데이터를 넣으려면 객체 필드(instance field)를 선언하고 생성자를 통해 받은 데이터를 그 필드에 저장하면 된다. enum은 원래 변경 불가능하므로(immutable) 모든 필드는 final로 선언되어야 한다(규칙15). 필드는 public으로 선언할 수도 있지만, private로 선언하고 public 접근자(accessor)를 두는 편이 더 낫다(규칙14).

일반적으로 유용하게 쓰일 enum이라면, 최상위(top-level) public 클래스로 선언해야 한다. 특정한 최상위 클래스에만 쓰이는 enum이라면 해당 클래스의 멤버 클래스로 선언해야 한다(규칙22).
