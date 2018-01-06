## 규칙51. 문자열 연결 시 성능에 주의하라

문자열 연결(concatenation) 연산자 +는 여러 문자열을 하나로 합하는 편리한 수단이다. 하지만 n개의 문자열에 연결 연산자를 반복 적용해서 연결하는데 드는 시간은 n^2에 비례한다.

```java
public String statement() {
    String result = "";
    for (int i = 0; i < numItems(); i++) {
        result += lineForItem(i);   // String concatenation
    }
    return result;
}
```

만족스런 성능을 얻으려면 String 대신 StringBuiler를 써야된다.

```java
public String statement() {
    StringBuilder b = new StringBuilder(numItems() * LINE_WIDTH);
    for (int i = 0; i < numItems(); i++) {
        b.append(lineForItem(i));
    }
    return b.toString();
}
```

**(JDK 1.5 부터는 String을 사용하더라도 컴파일 시 내부적으로 StringBuilder로 변경되어 성능차이가 없어졌습니다.)**
