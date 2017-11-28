## 규칙25. 배열 대신 리스트를 써라

배열은 제네릭 자료형과 두 가지 중요한 차이점을 갖고 있다.

### 1. 배열은 공변 자료형(convariant)인 반면, 제네릭은 불변 자료형(invariant)이다

Sub가 Super의 하위 자료형(subtype)이면 Sub[]도 Super[]의 하위 자료형이다.

```java
// 실행 중에 문제를 일으킴
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in" // ArrayStoreException 예외 발생
```

Type1과 Type2가 있을 때, List&lt;Type1&gt;은 List&lt;Type2&gt;의 상위 자료형이나 하위 자료형이 될 수 없다.

```java
// 컴파일 되지 않는 코드
List<Object> ol = new ArrayList<Long>(); // 자료형 불일치
ol.add("I don't fit in");
```

### 2. 배열은 실체화(reification)가 되는 자료형인 반면, 제네릭은 삭제(erasure) 과정을 통해 구현된다

- 배열의 각 원소의 자료형은 실행시간(runtime)에 결정된다. 
- 제네릭의 자료형에 관계된 조건들은 컴파일 시점에만 적용되고, 그 각 원소의 자료형 정보는 프로그램이 실행될 때는 삭제된다.

이런 기본적인 차이점 때문에 배열과 제네릭은 섞어 쓰기 어렵다.

```java
// 제네릭 배열 생성이 허용되지 않는 이유 - 아래의 코드는 컴파일되지 않는다
List<String>[] stringLists = new List<String>[1];
List<Integer> intList = Arrays.asList(42);
Object[] objects = stringLists;
objects[0] = intList;
String s = stringLists[0].get(0);
```

제네릭 자료형에 담긴 원소들의 자료형으로 만든 배열을 만든 배열을 반환하는 것은 일반적으로 불가능하다(규칙29에 부분적인 해결방안을 제시했다)

제네릭 배열 생성 오류에 대한 가장 좋은 해결책은 보통 E[] 대신 List&lt;E&gt;를 쓰는 것이다. 성능이 저하되거나 코드가 길어질 수는 있겠으나, 형 안전성과 호환성은 좋아진다.

### 요약

- 제네릭과 배열이 따르는 자료형 규칙은 서로 많이 다르다
  - 배열은 공변 자료형이자 실체화 가능 자료형이다
  - 제네릭은 불변 자료형이며, 실행시간에 형인자의 정보는 삭제된다
- 배열은 컴파일 시간에 형 안정성을 보장하지 못하며, 제네릭은 그 반대다
- 일반적으로 배열과 제네릭은 쉽게 혼용할 수 없다
- 만일 배열과 제네릭을 뒤섞어 쓰다가 컴파일 오류나 경고 메시지를 만나게 되면, 배열을 리스트로 바꿔야겠다는 생각이 본능적으로 들어야 한다
