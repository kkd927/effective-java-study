/* Created by rladnswl30 */

## 규칙34. 확장 가능한 enum을 만들어야 한다면 인터페이스를 이용하라.
__enum 자료형__ 은 __형 안전 enum__ 패턴보다 거의 모든 면에서 월등하다.  
그러나 __형 안전 enum__ 패턴은 계승을 통한 확장이 가능했던 반면, __enum 자료형__ 은 그렇지 않다.  
이를 해결하기 위한 것이 바로 __enum 자료형의 인터페이스 구현__ 이다.

[형 안전(typesafe) enum pattern ?](http://docs.oracle.com/javase/1.5.0/docs/guide/language/enums.html)

```JAVA
// 인터페이스를 이용해 확장 가능하게 만든 enum 자료형
public interface Operation {
  double apply(double x, double y);
}

public enum BasicOperation implements Operation {
  PLUS("+") {
    public double apply(double x, double y) { return x + y; }
  },
  MINUS("-") {
    public double apply(double x, double y) { return x - y; }
  },
  TIMES("*") {
    public double apply(double x, double y) { return x * y; }
  },
  DEVIDE("/") {
    public double apply(double x, double y) { return x / y; }
  };

  private final String symbol;

  BasicOperation(String symbol) {
    this.symbol = symbol;
  }

  @Override
  public String to String() {
    return symbol;
  }
}
```

BasicOperation은 enum자료형이라 계승할 수 없지만, Operation은 인터페이스라 확장이 가능하다.  
기본 연산을 확장해서 새로운 enum 자료형을 다음과 같이 만들 수 있다.

```JAVA
// 인터페이스를 이용해 기존 enum 자료형을 확장하는 사례
public enum ExtendedOperation implements Operation {
  EXP("-") {
    public double apply(double x, double y) { return Math.pow(x, y); }
  },
  REMAINDER("%") {
    public double apply(double x, double y) { return x % y; }
  }

  private final String symbol;

  ExtendedOperation(String symbol) {
    this.symbol = symbol;
  }

  @Override
  public String to String() {
    return symbol;
  }
}
```

기존 enum 자료형이 쓰인 곳에는 확장된 enum 자료형을 통째로 전달한다.

### 확장된 enum 자료형을 사용하는 첫번째 방법
```JAVA
public static void main(String[] args) {
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  test(ExtendedOperation.class, x, y);
}

private static <T extends Enum<T> & Operation> void test(
    Class<T> opSet, double x, double y) {
      for (Operation op : opSet.getEnumConstants()) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
      }
}
```
- ExtendedOperation.class는 한정적 자료형 토큰([규칙29](/Chapter5/Rule29.md) - 한정적 형인자나 한정적 와일드카드를 사용해서 표현 가능한 자료형을 제한) 구실을 한다.
- opSet의 형인자 T는 Class 객체가 나타내는 자료형이 enum자료형인 동시에 Operation의 하위 자료형이 되도록 한다.

### 확장된 enum 자료형을 사용하는 두번째 방법
```JAVA
public static void main(String[] args) {
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  test(Arrays.asList(ExtendedOperation.values()), x, y);
}

private static void test(Collection<? extends Operation> opSet, double x, double y) {
      for (Operation op : opSet) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
      }
}
```
- 이 경우, EnumSet이나 EnumMap(규칙32, 규칙33)을 사용할 수 없어서,  
여러 자료형에 정의한 연산들을 함께 전달할 필요가 없다면 위 예시를 이용하는 것이 낫다.

### 요약
- 계승 가능 enum 자료형은 만들 수 없지만, __인터페이스__ 를 만들고 그 인터페이스를 구현하는 기본 enum 자료형을 만들면 계승 가능 enum 자료형을 흉내낼 수 있다.
