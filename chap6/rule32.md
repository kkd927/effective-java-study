## Rule 32. 비트 필드(bit field) 대신 EnumSet을 사용하라

- 전통적인 int enum 패턴

```JAVA
// 비트 필드 열거형 상수 - 이제는 피해야 할 구현법
public class Text {
  public static final int STYLE_BOLD = 1 << 0; //1
  public static final int STYLE_ITALIC = 1 << 1; //2
  public static final int STYLE_UNDERLINE = 1 << 2; //4
  public static final int STYLE_STRIKETHROUGH = 1 << 3; //8

  // 이 베서드의 인자는 STYLE_상수를 비트별 or 한 값이거나 0.
  public void applyStyles(int styles) {...}
}
```
이렇게 하면 상수들을 집합(비트필드)에 넣을 때 비트별 or 연산을 사용할 수 있다. ``` text.applyStyles(STYLE_BOLD | STYLE_ITALIC) ```

But! int enum 패턴과 동일한 단점을 가지고 있다. 그러니까 **EnumSet** 사용하자!

#### EnumSet

- 특정한 enum 자료형의 값으로 구성된 집합을 효율적으로 표현할 수 있다.
- Set 인터페이스를 구현
- 형 안전성
- 내부적으로는 bit vector 사용. enum의 개수가 64 이하인 경우 EnumSet은 long 값 하나만 사용 (대부분) => 비트필드에 필적하는 성능

 ```JAVA
 // EnumSet - 비트필드를 대신할 현대적 기술
 public class Test {
   public enum Style {BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}

   // 어떤 Set 객체도 인자로 전달할 수 있으나 EnumSet 이 분명 최선
   public void applyStyles(Set<Style> styles) {...}
 }
```

```JAVA
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

**열거 자료형을 집합에 사용해야 한다고해서 비트 빌드로 표현하면 곤란하다.**

EnumSet의 단점 -immutable 객체를 만들 수 없다.
