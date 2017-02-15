## 규칙13. 클래스와 멤버의 접근 권한은 최소화하라
잘 설계된 모듈과 그렇지 못한 모듈을 구별 짓는 가장 중요한 속성 하나는 모듈 내부의 데이터를 비롯한 구현 세부사항을 다른 모듈에 잘 감추느냐의 여부다.

잘 설계된 모듈은 구현 세부사항을 전부 API 뒤쪽에 감춘다. 정보은닉(information hiding) 또는 캡슐화(encapsulation)라는 용어로 알려져 있다.

정보은닉은 시스템을 구성하는 모듈 사이의 의존성을 낮춘다(decouple).

### 1. 각 클래스와 멤버는 가능한 접근 불가능하도록 만들어야 한다

- 최상위 레벨 클래스나 인터페이스는 가능한 package-private로 선언
- public으로 선언하게 되면 호환성을 보장하기 위해 해당 개체를 계속 지원해야된다
- package-private로 선언된 최상위 클래스나 인터페이스를 사용하는 클래스(사용자 클래스)가 하나뿐이라면 사용자 클래스의 private 중첩 클래스로 고려
- 필드나 메서드, 중첩 클래스(nested class), 중첩 인터페이스(neseted interface) 같은 멤버의 접근 권한은 최대한 private (꼭 다른 클래스가 사용해야 하는 경우만 package-private)
- public 클래스의 멤버들은 protected 사용을 자제(최대한 package-private)

### 2. 객체 필드(instance field)는 절대로 public으로 선언하면 안된다

- 비-final 필드나 변경 가능 객체에 대한 final 참조 필드를 public으로 선언하면, 필드에 저장될 값을 제한 불가 (불변식을 강제 못함)
- 변경 가능 public 필드를 가진 클래스는 다중 스레드에 안전하지 않다

### 3. public static final 필드를 제외한 어떤 필드도 public으로 선언하지 마라

- 어떤 상수들이 클래스로 추상화된 결과물의 핵심적 부분을 구성한다고 판단되는 경우, 해당 상수를 public static final 필드로 선언하여 공개(대문자로와 밑줄로만 구성하는 것이 관습)
- 이런 경우 반드시 기본 자료형 값들을 갖거나, 변경 불가능 객체를 참조
- public static final 배열 필드를 두거나, 배열 필드를 반환하는 접근자(accssor)를 정의하면 안된다(길이가 0이 아닌 배열은 언제나 변경가능)

```java
// 보안 문제를 초래할 수 있는 코드
public static final Thing[] VALUES = { ... };
```

- 방안1) public으로 선언되었던 배열을 private 로 바꾸고, 변경 불가능한 public 리스트를 만든다.
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES = 
  Collections.unmodifableList(Arrays.asList(PRIVATE_VALUES));
```

- 방안2) 배열은 private로 선언하고, 해당 배열을 복사해서 반환하는 public 메서드 추가
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values() {
  return PRIVATE_VALUES.clone();
}
```



### 요약
- 접근 권한은 가능한 낮춰라
- 최소한의 public API를 설계한 다음 나머지는 API에서 제외하라
- public static final 필드를 제외한 어떤 필드도 public으로 선언하지 마라
- public static final 필드가 참조하는 객체는 변경 불가능 객체로 만들라

