## 규칙46. for 문보다는 for-each 문을 사용하라

릴리스 1.5 전에는 컬렉션을 순회할 때 아래의 숙어를 따르는 것이 바람직했다.

```java
// 컬렉션 순회를 위해 한동안 많이 썼던 숙어
for (Iterator i = c.iterator(); i.hasNext();) {
    doSomething((Element) i.next());
}

// 배열을 순회할 때 한동안 많이 사용한 숙어
for (int i = 0; i < a.length; i++) {
    doSomething(a[i]);
}
```

릴리스 1.5부터 도입된 for-each 문은 성가신 코드를 제거하고 반복자나 첨자 변수를 완전히 제거해서 오류 가능성을 없앤다.

```java
// 컬렉션이나 배열을 순회할 때는 이 숙어를 따르자
for (Element e : elements) {
    doSomething(e);
}
```

for-each 문의 장점은 여러 컬렉션에 중첩되는 순환문을 만들어야 할 때 더 빛난다. for-each 문으로는 컬렉션과 배열뿐 아니라 Iterable 인터페이스를 구현하는 어떤 객체도 순회할 수 있다.

```java
public interface Iterable<E> {
    // 이 Iterable 안에 있는 원소들에 대한 반복자 반환
    Iterator<E> iterator();
}
```

요약하자면, for-each 문은 전통적인 for 문에 비해 명료하고 버그 발생 가능성도 적으며, 성능도 for 문에 뒤지지 않는다. 그러니 가능하면 항상 사용해야 한다. 그러나 불행히도 아래의 세 경우에 대해서는 for-each 문을 적용할 수 없다.

1. 필터링(filtering) - 컬렉션을 순회하다가 특정 원소를 삭제할 필요가 있다면, 반복자를 명시적으로 사용해야 한다. 반복자의 remove 메서드를 호출해야 하기 때문이다.

2. 변환(transforming) - 리스트나 배열을 순회하다가 그 원소 가운데 일부 또는 전부의 값을 변경해야 한다면, 원소의 값을 수정하기 위해서 리스트 반복자나 배열 첨자가 필요하다.

3. 병렬 순회(parallel iteration) - 여러 컬렉션을 병렬적으로 순회해야 하고, 모든 반복자나 첨자 변수가 발맞춰 나아가도록 구현해야 한다면 반복자나 첨자 변수를 명시적으로 제어할 필요가 있을 것이다.
