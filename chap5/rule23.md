## 규칙23. 새 코드에는 무인자 제네릭 자료형을 사용하지 마라

선언부에 형인자(type parameter)가 포함된 클래스나 인터페이스는 제네릭(generic) 클래스나 인터페이스라 부르고, 이 둘은 __제네릭 자료형(generic type)__ 이라 부른다.

각 제네릭 자료형은 __형인자 자료형(parameterized type)__ 집합을 정의한다. List&lt;String&gt;는 원소 자료형이 String인 리스트를 나타내는 형인자 자료형이다.

또한 각 제네릭 자료형은 __무인자 자료형(raw type)__ 을 정의할 수 도있다. List&lt;E&gt;의 무인자 자료형은 List다.

```java
// 무인자 컬렉션 자료형
private final Collection stamps = ...;

// 실수로 넣은 Coin 객체
stamps.add(new Coin( ... ));

for (Iterator i = stamps.iterator(); i.hasNext(); ) {
  Stamp s = (Stamp) i.next(); // ClassCastException 발생
  ...
}
```

위와 같이 무인자 자료형 stamps에 잘 못 들어간 Coin 객체 때문에 실행시간에 예외가 발생할 수 있다. 제네릭을 쓰면, 컴파일러에게 컬렉션에 담길 객체의 자료형이 무엇인지 선언할 수 있다.

```java
// 형인자 컬렉션 자료형 - 형 안전성(typesafe) 확보
private final Collection<Stamp> stamps = ...;
```

형인자 자료형으로 선언하면, 엉뚱한 자료형의 객체를 넣는 코드를 컴파일 할 때 무엇이 잘못됐는지 확인할 수 있는 오류 메시지가 출력된다. 게다가 컬렉션에서 원소를 꺼낼 때 형변환을 하지 않아도 된다.

```java
// 형변환 불필요
for (Stamp s : stamps) {
  ...
}
```

무인자 자료형을 쓰면 형 안전성이 사라지고, 제네릭의 장점 중 하나인 표현력(expressiveness) 측면에서 손해를 보게 된다.

List와 같은 무인자 자료형을 쓰면 안 되지만, 아무 객체나 넣을 수 있는 List&lt;Object&gt; 같은 자료형은  써도 좋다. List와 같은 무인자 자료형을 사용하면 형 안정성을 읽게 되지만, List&lt;Object&gt;와 같은 형인자 자료형을 쓰면 그렇지 않다.

```java
public static void main(String[] args) {
  List<String> strings = new ArrayList<String>();
  unsafeAdd(strings, new Integer(42));
  String s = strings.get(0); // 에러 발생
}

// 실행 도중 오류를 일으키는 무인자 자료형
private static void unsafeAdd(List list, Object o) {
  list.add(o);
}
```

List를 List&lt;Object&gt;로 바꾼 다음 다시 컴파일 해 보면, 더 이상 컴파일이 되지 않는다.

```java
private static void unsafeAdd(List<Object> list, Object o) {
  list.add(o);
}
```

버전 1.5부터 자바는 __비한정적 와일드카드 자료형(unbounded wildcard type)__ 이라는 좀더 안전한 대안을 제공한다. 실제 형 인자가 무엇인지는 모르거나 신경 쓰고 싶지 않을 때는 형 인자로 __'?'__ 를 쓰면 된다.

비한정적 와일드카드 자료형을 쓰면 null 이외에 다른 타입의 원소를 넣을 수 없다. (예를 들어 List&lt;?&gt;에 처음 String 객체가 들어갔다면 null과 String 이외의 다른 타입의 원소가 들어갈 수 없다.)

이런 제약이 불만이라면 제네릭 메서드([규칙27](rule27.md))를 사용하거나, __한정적 와일드카드 자료형(bounded wildcard types)__ 을 쓰면 된다([규칙28](rule28.md)).

### 예외

새로 만든 코드에는 무인자 자료형을 쓰면 안 된다고 했지만, 그 규칙에도 사소한 예외 두가지가 있다.

첫 번째는 클래스 리터럴(class literal)에는 반드시 무인자 자료형을 사용해야 한다는 것이다. 클래스 리터럴에는 형인자 자료형을 쓸 수 없다. (List.class, String[].class, int.class는 가능하지만 List&lt;String&gt;.class나 List&lt;?&gt;.class는 불가)

두 번째는 제네릭 자료형 정보는 프로그램이 실행될 때는 지워지기 때문에, instanceof 연산자는 비한정적 와일드카드 자료형 이외의 형인자 자료형에 적용할 수 없다.

```java
if (o instanceof Set) {
  Set<?> m = (Set<?>) o;
}
```

제네릭 자료형에 instanceof 연산자를 적용할 때는 위와 같이 무인자 자료형을 통해 객체의 타입를 확인한 후 와일드카드 자료형으로 형변환 하는 것이 좋다.

### 요약

| 용어 | 예 |
| --- | --- |
| 형인자 자료형(parameterized type) | List&lt;String&gt; |
| 실 형인자(actual parameter) | String |
| 제네릭 자료형(generic type) | List&lt;E&gt; |
| 형식 형인자(formal type parameter) | E |
| 비한정적 와일드카드 자료형(unbounded wildcard type) | List&lt;?&gt; |
| 한정적 와일드카드 자료형(bounded wildcard type) | List&lt;? extends Number&gt; |
| 무인자 자료형(raw type) | List |
| 한정적 형인자(bounded type parameter) | &lt;E extends Number&gt; |
| 재귀적 형 한정(recursive type bound) | &lt;T extends Comparable&lt;T&gt;&gt; |
| 제네릭 메서드(generic method) | static &lt;E&gt; List&lt;E&gt; asList(E[] a) |
| 자료형 토큰(type token) | String.class |

