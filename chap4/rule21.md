## 규칙21. 전략을 표현하고 싶을 때는 함수 객체를 사용하라

프로그래밍 언어 가운데는 함수 포인터(function pointer), 대리자(delegate), 람다(lamda expression) 처럼, 특정 함수를 호출할 수 있는 능력을 저장하고 전달할 수 있도록 하는 것들이 있다. 

예를 들어 C 표준 라이브러리에 포함되어 있는 qsort 함수는 비교자(comparator) 함수에 대한 포인터를 인자로 받아 정렬할 요소들을 비교한다. 이것은 전략(strategy) 패턴의 한 사례로 비교자 함수를 통해 정렬 전략을 표현하는 것이다.

자바는 함수 포인터를 지원하지 않는다. 하지만 객체 참조를 통해 비슷한 효과를 달성할 수 있다. 가지고 있는 메서드가 다를 객체에 작용하는 메서드 하나 뿐인 객체는 해당 메서드의 포인터 구실을 한다. 이런 객체를 __함수 객체(function object)__ 라고 부른다.

```java
class StringLengthComparator {
  public int compare(String s1, String s2) {
    return s1.length() - s2.length();
  }
}
```

위의 예제는 문자열을 비교하는 데 사용될 수 있는, 실행 가능 전략(concrete strategy)이다. 필드가 없고 그 모든 객체는 기능적으로 동일한 무상태(stateless) 클래스다. 싱글턴 패턴(singleton pattern)을 따르면 쓸데없는 객체 생성은 피할 수 있다.([규칙3](/chap2/rule3.md), [규칙5](/chap2/rule5.md))

```java
class StringLengthComparator {
  private StringLengthComparator() {}
  public static final StringLengthComparator INSTANCE = new StringLengthComparator();

  public int compare(String s1, String s2) {
    return s1.length() - s2.length();
  }
}
```

__실행 가능 전략 클래스(concrete strategy class)__ 객체를 메서드에 전달하기 위해서는 인자의 자료형이 맞아야 한다. 따라서 클래스에 어울리는 __전략 인터페이스(strategy interface)__를 정의할 필요가 있다.

```java
// T를 형식 자료형 인자(formal type parameter)라고 부른다
public interface Comparator<T> {
  public int compare(T t1, T t2);
}
```

실행 가능 전략 클래스는 익명 클래스(anonymouse class)로 정의하는 경우도 많다([규칙22](rule22.md)).

```java
Arrays.sort(stringArray, new Comparator<String>() {
  public int compare(String s1, String s2) {
    return s1.length() - s2.length();
  }
});
```

여러번 수행되는 클래스라면 함수 객체를 private static final 필드에 저장하고 재사용하는 것을 고려해야 한다.

```java
// java.lang.String 
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
  public static final Comparator<String> CASE_INSENSITIVE_ORDER = new CaseInsensitiveComparator();

  // 대소문자 구별 없는 문자열 비교자
  private static class CaseInsensitiveComparator implements Comparator<String>, java.io.Serializable {
    private static final long serialVersionUID = 8575799808933029326L;

    public int compare(String s1, String s2) {
      int n1 = s1.length();
      int n2 = s2.length();
      int min = Math.min(n1, n2);
      for (int i = 0; i < min; i++) {
        char c1 = s1.charAt(i);
        char c2 = s2.charAt(i);
        if (c1 != c2) {
          c1 = Character.toUpperCase(c1);
          c2 = Character.toUpperCase(c2);
          if (c1 != c2) {
              c1 = Character.toLowerCase(c1);
              c2 = Character.toLowerCase(c2);
              if (c1 != c2) {
                // No overflow because of numeric promotion
                return c1 - c2;
              }
          }
        }
      }
      return n1 - n2;
    }
  }

  ...
}
```

전략 인터페이스는 실행 가능 전략 객체들의 자료형 구실을 한다. 따라서 실행 가능 전략 클래스는 굳이 public으로 만들어 공개할 필요가 없다. 대신, 전략 인터페이스가 자료형인 public static 필드들을 갖는 __호스트 클래스(host class)__를 정의하는 것도 방법이다.

### 요약

- 함수 객체의 주된 용도는 전략 패턴(strategy pattern)을 구현하는 것
- 이 패턴을 구현하기 위해서는 전략을 표현하는 인터페이스를 선언하고, 실행 가능 전략 클래스가 전부 해당 인터페이스를 구현하도록 해야 한다
- 실행 가능 전략이 한 번만 사용되는 경우에는 보통 그 전략을 익명 클래스 객체로 구현한다
- 반복적으로 사용된다면 private static 멤버 클래스로 전략을 표현한 다음, 전략 인터페이스가 자료형인 public static final 필드를 통해 외부에 공개하는 것이 바람직
