## 규칙22. 멤버 클래스는 가능하면 static으로 선언하라

중첩 클래스(nested class)는 다른 클래스 안에 정의된 클래스다. 중첩 클래스에는 정적 멤버 클래스), 비-정적 멤버 클래스, 익명 클래스, 지역 클래스 이렇게 네 가지 종류가 있다. 첫 번째를 제외한 나머지는 전부 내부 클래스(inner class)다.

### 정적 멤버 클래스 (static member class)

- 바깥 클래스의 모든 멤버에(private로 선언된 것까지도) 접근할 수 있다
- 바깥 클래스의 정적 멤버이며, 다른 정적 멤버와 동일한 접근 권한 규칙(accessibility rule)을 따른다
- 바깥 클래스와 함께 사용할 때만 유용한 public 도움 클래스(helper class)를 정의하는데 자주 사용

### 비-정적 멤버 클래스 (nonstatic member class)

- 바깥 클래스 객체와 자동적으로 연결된다
- 비-정적 멤버 클래스 안에서 바깥 클래스의 메서드를 호출할 수 있고, this 한정(qualified this) 구문을 통해 바깥 객체에 대한 참조를 획득할 수도 있다

```java
class Envelope {
  void x() { System.out.println("Hello, World!"); }

  class Enclosure {
    void x() { 
      // this 한정 구문
      Envelope.this.x();
    }
  }
}
```

- 비-정적 멤버 클래스의 객체는 바깥 클래스 객체 없이는 존재할 수 없다
- 비-정적 멤버 클래스 객체와 바깥 객체와의 연결을 비-정적 멤버 클래스의 객체가 만들어지는 수간에 확립되고, 그 뒤에는 변경할 수 없다
- 보통 연결은 바깥 클래스의 객체 메서드(instance method) 안에서 비-정적 멤버 클래스의 생서자를 호출하는 수간 자동으로 만들어진다


- 어댑터(Adapter)르 정의할 때 많이 쓰인다

```java
// 비-정적 멤버 클래스의 전형적 용례
public class MySet<E> extends AbstractSet<E> {
  ...

  public Iterator<E> iterator() {
    return new MyIterator();
  }

  private class MyIterator implements Iterator<E> {
    ...
  }
}
```

- 바깥 클래스 객체에 접근할 필요가 없는 멤버 클래스를 정의할 때는 항상 선언문 앞에 static을 붙여서 비-정적 멤버 클래스 대신 정적 멤버 클래스로 만들어야 한다
- private 정적 멤버 클래스는 바깥 클래스 객체의 컴포넌트(component)를 표현하는 데 흔히 쓰인다 (ex. Map 구현 클래스의 Entry 객체)

### 익명 클래스 (anonymous class)

- 익명 클래스는 바깥 클래스의 멤버가 아니다
- 사용하는 순간에 선언하고 객체를 만든다
- 비-정적 문맥(nonstatic context) 안에서 사용될 때만 바깥 객체를 갖는다
- 정적 문맥(static context) 안에서 사용된다 해도 static 멤버를 가질 수 없다


- 함수 객체([규칙21](rule21.md))를 정의할 때 널리 쓰인다
- Runnable, Thread, TimerTask 객체 같은 프로세스 객체(process object)를 만드는 데도 널리 쓰인다
- 정적 팩터리 메서드 안에서도 많이 쓰인다

```java
static List<Integer> intArrayAsList(final int[] a) {
    if (a == null) {
        throw new NullPointerException();
    }

    return new AbstractList<Integer>() {
        public Integer get(int i) {
            return a[i];
        }

        @Override
        public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }

        public int size() {
            return a.length;
        }
    }
}
```

### 지역 클래스 (local class)

- 네 종류의 중첩 클래스 가운데 사용 빈도가 가장 낮다
- 지역 변수(local variable)가 선언될 수 있는 곳이라면 어디서든 선언할 수 있으며, 지역변수와 동일한 유효범위 규칙(scoping rule)을 따른다
- 익명 클래스처럼 비-정적 문맥에서 정의했을 때만 바깥 객체를 갖는다
- static 멤버는 가질 수 없다
- 길어지면 가독성을 해치므로, 익명 클래스처럼 짧게 작성해야 한다


### 요약

- 중첩 클래스에는 네 가지 종류가 있고, 그 각각은 어울리는 자리가 다르다
- 중첩 클래스를 메서드 밖에서 사용할 수 있어야 하거나, 메서드 안에 놓기에는 너무 길 경우에는 __멤버 클래스__ 로 정의하라
- 멤버 클래스의 객체 각각이 바깥 객체에 대한 참조를 가져야 하는 경우에는 __비-정적 멤버 클래스__ 로 만들어라
  - 그렇지 않은 경우에는 __정적 멤버 클래스__ 로 만들면 된다
- 중첩 클래스가 특정한 메서드에 속해야 하고, 오직 한 곳에서만 객체를 생성하며, 해당 중첩 클래스의 특성을 규정하는 자료형이 이미 있다면 __익명 클래스__ 로 만들어라
  - 그렇지 않을 때는 __지역 클래스__ 로 만들면 된다
