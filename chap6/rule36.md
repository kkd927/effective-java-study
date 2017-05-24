## 규칙36. Override 어노테이션은 일관되게 사용하라

Override 어노테이션을 일관되게 사용하면 끔찍한 버그들을 방지할 수 있다.

```java
public class Bigram {
  private final char first;
  private final char second;

  public Bigram(char first, char second) {
    this.first = first;
    this.second = second;
  }

  public boolean equals(Bigram b) {
    return b.first == first && b.second == second;
  }

  public int hashCode() {
    return 31 * first + second;
  }

  public static void main(String[] args) {
    Set<Bigram> s = new HashSet<Bigram>();
    for (int i = 0; i < 10; i++)
      for (char ch = 'a'; ch <= 'z'; ch++)
        s.add(new Bigram(ch, ch));
    System.out.println(s.size());
  }
}
```

Object의 equals를 재정의하기 위해서는 equals를 선언할 때 인자의 자료형을 Object로 해야하는데, Bigram의 equals 메서드를 보면 자료형이 Bigram으로 되어 있다.

```@Override``` 어노테이션을 통해 컴파일러가 이런 오류를 찾을 수 있게 할 수 있다.

```java
@Override
public boolean equals(Object o) {
  if (!(o instanceof Bigram))
    return false;
  Bigram b = (Bigram) o;
  return b.first == first && b.second == second;
}
```

따라서, 상위 클래스에 선언된 메서드를 재정의할 때는 반드시 선언부에 Override 어노테이션을 붙어야 한다. 

비-abstract 클래스에서 abstract 메서드를 재정의할 때는 Override 어노테이션을 붙이지 않아도 된다. 자바 1.6이상을 사용한다면, 인터페이스에 선언된 메서드를 구현할 때도 Override를 사용할 수 있다.

### 요약 

- 상위 자료형에 선언된 메서드를 재정의하는 모든 메서드에 Override 어노테이션을 붙이도록 하면 굉장히 많은 오류를 막을 수 있다.
- 예외적으로, 비-abstract 클래스에서 상위 클래스의 abstract 메서드를 재정의할 때는 Override 어노테이션을 붙이지 않아도 된다(붙여도 나쁠 것은 없다)
