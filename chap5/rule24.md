## 규칙24. 무점검 경고(unchecked warning)를 제거하라

제네릭으로 프로그램하다 보면 많은 컴파일러 경고 메시지를 보게 된다. 

무점검 형변환 경고(unchecked cast warning), 무점검 메서드 호출 경고(unchecked method invocation warning), 무점검 제네릭 배열 생성 경고(unchecked generic array creation warning), 그리고 무점검 변환 경고(unchecked conversion warning) 등이 있다.

코드의 형 안전성(typesafe) 보장을 위해 모든 무점검 경고는, 가능하다면 없애야 한다. 제거할 수 없는 경고 메시지는 형 안정성이 확실할 때만 __@SuppressWarnings("unchecked")__ 어노테이션(annotation)을 사용해 억제해야 한다.

SupressWarnings 어노테이션은 개별 지역 변수 선언부터 클래스 전체에까지, 어떤 크기의 단위에도 적요할 수 있지만 가능한 작은 범위에 적용해야 한다. (중요한 경고 메시지를 놓치게 될 수 도 있다)


```java
// @SuppressWarnings범위는 항상 최소한으로
public <T> T[] toArray(T[] a) {
  if (a.length < size) {
    // 아래의 형변환은 배열의 자료형이 인자로 전달되 자료형인 T[]와 같으므로 정확하다
    @SuppressWarnings("unchecked") T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
    return result;
  }

  System.arraycopy(elements, 0, a, 0, size);
  if (a.length > size)
    a[size] = null;

  return a;
}
```

@SuppressWarnings("unchecked") 어노테이션을 사용할 때마다, 왜 형 안전성을 위반하지 않는지 밝히는 주석을 반드시 붙여야 한다.

### 요약

- 무점검 경고(unchecked warning)는 중요하다
- 모든 무점검 경고는 프로그램 실행 도중에 ClassCastException이 발생할 가능성을 나타낸다
- 무점검 경고 메시지는 최선을 다해 제거하라
- 경고를 제거할 수 없으나 형 안전성을 보장한다는 사실을 입증할 수 있다면, @SuppressWarnings("unchecked") 어노테이션을 사용하되, 적용 범위는 최소화해야 한다
- 경고 메시지를 억제한 경우, 그 이유를 반드시 주석에 써야 한다
