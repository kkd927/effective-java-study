## 규칙26. 가능하면 제네릭 자료형으로 만들 것

제네릭 자료형을 직접 만드는 것은 까다로운데, 배워둘 만한 가치는 있다.

```java
// Object를 사용한 컬렉션 - 제네릭을 적용할 중요 후보
public class Stack {
  private Object[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;

  public stack() {
    elements = new Object[DEFAULT_INITIAL_CAPACITY];
  }

  public void push(Object e) {
    ensureCapacity();
    elements[size++] = e;
  }

  public Object pop() {
    if (size == 0)
      throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // 만기 참조 제거
    return result;
  }

  public boolean isEmpty() {
    return size == 0;
  }

  private void ensureCapacity() {
    if (elements.length == size)
      elements = Arrays.copyOf(elements, 2 * size + 1);
  }
}
```

클래스를 제네릭화하는 첫 번째 단계는 선언부에 형인자(type parameter)를 추가하는 것이다. 관습적으로 이름은 E라고 붙인다(규칙56). 다음 단계는 Object를 자료형으로 사용하는 부분들을 전부 찾아서, 형인자 E로 대체하고 컴파일해 보는 것이다.

```java
// 제네릭을 사용해 작성한 최초 Stack 클래스 - 컴파일되지 않는다
public class Stack<E> {
  private E[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;

  public stack() {
    elements = new E[DEFAULT_INITIAL_CAPACITY];
  }

  public void push(E e) {
    ensureCapacity();
    elements[size++] = e;
  }

  public E pop() {
    if (size == 0)
      throw new EmptyStackException();
    E result = elements[--size];
    elements[size] = null; // 만기 참조 제거
    return result;
  }

  // ... 이하 동일
}
```
보통 하나 이상의 오류나 경고 메시지를 만나게 될 것이다.

```java
// 오류
  Stack.java:8: generic array creation
          elements = new E[DEFAULT_INITIAL_CAPACITY];
```

[규칙25](rule25.md)에서 설명한 대로, __E__ 같은 실체화 불가능 자료형으로는 배열을 생성할 수 없다.

첫번째 해결책은 Object의 배열을 만들어서 제네릭 배열 자료형으로 형변환하는 방법이다. 문법적으로는 문제는 없지만, 일반적으로 형 안정성을 보장하는 방법은 아니다.

```java
elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
```

무점검 형변환(unchecked cast)을 하기 전에 개발자는 반드시 그런 형변환이 프로그램의 형 안정성을 해치지 않음을 확실히 해야 한다. 무점검 형변환이 안전함을 증명했다면, 경고를 억제하되 범위는 최소한으로 줄여야 한다.([규칙24](rule24.md))

```java
// elements 배열에 push(E)를 통해 전달된 E 형의 객체만 저장된다.
// 이 정도면 형 안전성은 보장할 수 있지만, 배열의 실행시간 자료형은 E[]가
// 아니라 항상 Object[]이다!
@SuppressWarnings("unchecked")
public Stack() {
  elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
}
```

두번째 해결책은 elements의 자료형을 E[]에서 Object[]로 바꾸는 것이다.

```java
// 오류
Stack.java:19: incompatible types
found   : Object,   required: E
      E result = elements[--size];
}
```

배열에서 꺼낸 원소의 자료형을 Object에서 E로 변환하도록 코드를 수정하면, 이 오류는 경로 바뀐다.

```java
Stack.java:19: warning: [unchecked] unchecked cast
found   : Object,  required: E
        E result = (E) elements[--size];
```

E는 실체화 불가능 자료형이므로 컴파일러는 이 형변환을 실행 중에 검사할 수 없다. 하지만 위의 무점검 형변환이 안전하다는 것, 그래서 경고를 억제해도 좋다는 것은 개발자 스스로 쉽게 입증할 수 있다.

```java
// 무점검 경고를 적절히 억제한 사례
public E pop() {
  if (size == 0)
    throw new EmptyStackException();

  // 자료형이 E인 원소만 push하므로, 아래의 형변환은 안전하다.
  @SuppressWarnings("unchecked") E result = (E) elements[--size];
  
  elements[size] = null; // 만기 참조 제거
  return result;
}
```

제네릭 배열 생성 오류를 피하는 두 방법 가운데 어떤 것을 쓸지는 대체로 취향 문제다. 첫 번째 해법을 적용하면 E[]로 한 번만 형변환을 하면 되겠지만, 두 번째 해법을 쓰면 코드 여기저기서 E로 형변환을 해야한다. 그래서 첫 번째 방법이 좀 더 보편적으로 쓰이는 이유다.

```java
// 제네릭 Stack의 사용 예
public static void main(String[] args) {
  Stack<String> stack = new Stack<String>();
  for (String arg : args)
    stack.push(arg);
  while (!stack.isEmpty())
    System.out.println(stack.pop().toUpperCase());
}
```

방금 살펴본 Stack과 같은 제네릭 자료형 거의 대부분은 형인자에 어떤 자료형도 쓸 수 있다. Stack&lt;Object&gt;, Stack&lt;int[]&gt;, Stack&lt;List&lt;String&gt;&gt; 등, 객체 참조에 사용할 수 있는 자료형이면 아무것이나 된다. 

기본 자료형은 자바 제네릭 자료형 시스템의 근본적 한계 때문에 사용이 불가능하지만, 이런 제약을 피하려면 객체화된 기본 자료형(boxed primitive type)을 사용하면 된다(규칙49).

### 요약

- 제네릭 자료형은 클라이언트가 형변환을 해야만 사용할 수 있는 자료형보다 안전할 뿐 아니라 사용하기도 쉽다
- 새로운 자료형을 설계할 때는 형변환 없이도 사용할 수 있도록 하라 (그러려면 제네릭 자료형으로 만들어야할 때가 많을 것이다)
- 시간 있을 때마다 기존 자료형을 제네릭 자료형으로 변환하라
- 기존 클라이언트 코드를 깨지 않고도 새로운 사용자에게 더 좋은 API를 제공할 수 있게 될 것이다([규칙23](rule23.md)).
