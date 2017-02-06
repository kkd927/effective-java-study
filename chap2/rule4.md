## 규칙4. 객체 생성을 막을 때는 private 생성자를 사용하라

java.lang.Math, java.util.Arrays, java.util.Collections 같은 ___유틸리티 클래스___(utility class)들은 객체를 만들 목적의 클래스가 아니다.

### private 생성자를 이용한 객체 생성 방지

```JAVA
public class UtilityClass {
  // 기본 생성자가 자동 생성되지 못하도록 하여 객체 생성 
  private UtilityClass() {
    throw new AssertionError();
  }
}
```

- AssertionError는 반드시 필요한 것은 아니지만, 클래스 안에서 실수로 생성자를 호출하면 바로 알 수 있게 하기 위한 것.
- 생성자를 명시적으로 정의했으나 직관적이진 않으니 위 처럼 주석을 달아 두는 것이 바람직하다.
- 호출 가능한 생성자가 없기 때문에 하위 클래스도 만들 수 없다.
