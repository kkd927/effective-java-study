## 규칙27. 가능하면 제네릭 메서드로 만들 것

__제네릭화(genericfication)__ 로 혜택을 보는 것은 클래스뿐만 아니다. 메서드도 혜택을 본다. static 유틸리티 메서드는 특히 제네릭화하기 좋은 후보다.

```java
// 무인자 자료형 사용 - 권할 수 없는 방법(규칙23)
public static Set union(Set s1, Set s2) {
  Set result = new HashSet(s1);
  result.addAll(s2);
  return result;
}
```

형인자를 선언하는 __형인자 목록(type parameter list)__ 은 메서드의 수정자(modifier)와 반환값 자료형 사이에 둔다.

```java
// 제네릭 메서드
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
  Set<E> result = new HashSet<E>(s1);
  result.addAll(s2);
  return result;
}
```

컴파일러는 union에 전달된 두 인자의 자료형으로 E의 자료형을 알아낼 수 있다. 이 과정을 __자료형 유추(type inference)__ 라 한다.

제네릭 생성자를 호출할 때는 형인자를 명시적으로 전달해야되는데, 성가신 일일 수 있다.

```java
// 생성자를 통한 형인자 자료형 객체 생성
Map<String, List<String>> anagrams =
  new HashMap<String, List<String>>();
```

이런 번거로움을 피하려면, 사용할 생성자마다 제네릭 정적 팩터리 메서드(generic static factory method)를 만들면 된다.

```java
// 제네릭 정적 팩터리 메서드
public static <K,V> HashMap<K,V> newHashMap() {
  return new HashMap<K,V>();
}

// 사용
Map<String, List<String>> anagrams = newHashMap();
```

자바 1.7부터는 제네릭 자료형의 생성자에도 자료형 추론이 지원된다([다이아몬드 연산자](http://www.javaworld.com/article/2074080/core-java/jdk-7--the-diamond-operator.html)).

```java
// 다이아몬드 연산자
Map<String, List<String>> anagrams = new HashMap<>();
```

상대적으로 사용 빈도가 낮긴 하나, 형인자가 포함된 표현식으로 형인자를 한정하는 것도 가능하다. 이런 용볍을 __재귀적 자료형 한정(recursive type bound)__ 이라 한다.

```java
// 재귀적 자료형 한정
public interface Comparable<T> {
  int compareTo(T o);
}
```

### 요약

- 제네릭 자료형이나 제네릭 메서드는 클라이언트가 직접 입력 값과 반환값의 자료형을 형변환해야 하는 메서드보다 사용하기 쉽고 형 안정성도 높다
- 자료형을 만들 때처럼, 새로운 메서드를 고안할 때는 형변환 없이도 사용할 수 있을지 살펴보라
(제네릭으로 만드는 것이 좋겠다는 판단을 내리게 될 때가 많을 것이다)
- 자료형의 경우와 마찬가지로, 시간 날 때 기존 메서드를 제네릭 메서드로 확장해 높으면 기존 클라이언트 코드를 깨지 않고도 새 사용자에게 더 좋은 API를 제공할 수 있게 될 것이다
