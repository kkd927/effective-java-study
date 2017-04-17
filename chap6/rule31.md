/* Created by choijaesun1 */
## ordinal 대신 객체 필드를 사용하라

ordinal 메서드 : enum 상수의 위치를 나타내는 정수값 반환

- ordinal을 남용한 사례

```JAVA
public enum Ensemble {
  SOLO, DUET, TRIO, QUARTET, QUINTET, SEXTET, SEPTET, OCTET, NONET, DECTET;

  public int numberOfMusicians() {
    return ordinal() +1;
  }
}

// 상수 순서를 변경하는 순간 numberOfMusicians 메서는 깨진다.
// 새로운 상수를 추가하기도 어렵다.
```

**그냥 객체 필드 사용하자**

```JAVA
public enum Ensemble {
  SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5) SEXTET(6), SEPTET(7), OCTET(8), NONET(9), DECTET(10),
  DOUBLE_QUARTET(8)...;

  private final int numberOfMusicians;
  Ensemble(int size) {
    this.numberOfMusicians = size;
  }

  public int numberOfMusicians() {
    return numberOfMusicians;
  }
  
}
```

enum 기반 (EnumSet, EnumMap) 자료구조를 만들 것이 아니라면 ordinal 메서드 사용늰늰
