## 규칙50. 다른 자료형이 적절하다면 문자열 사용은 피하라

문자열은 텍스트 표현과 처리에 걸맞도록 설계되었다. 그런데 문자열은 워낙 흔한 데다 자바의 문자열 지원도 아주 훌륭하기 때문에 원래 설계된 목적 외의 용도로 많이 활용하는 경향이 있다.

**문자열은 값 자료형(value type)을 대신하기에 부족하다.** 적절한 값 자료형이 있다면 그것이 기본 자료형이건 아니면 객체 자료형이건 상관없이 해당 자료형을 사용해야 한다.

**문자열은 enum 자료형을 대신하기에는 부족하다.** enum은 문자열보다 훨씬 좋은 열거 자료형 상수들을 만들어 낸다.

**문자열은 혼합 자료형(aggregate type)을 대신하기엔 부족하다.**

```java
// 문자열을 혼합 자료형으로 써먹은 부적절한 사례
String compoundKey = className + "#" + i.next();
```

**문자열은 권한(capability)을 표현하기엔 부족하다.**

```java
// 문자열 권한으로 사용하는 잘못된 예제
public class ThreadLocal {
    private ThreadLocal() {}

    // 주어진 이름이 가리키는 스레드 지역 변수의 값 설정
    public static void set(String key, Object value);

    // 주어진 이름이 가리키는 스레드 지역 변수의 값 반환
    public static Object get(String key);
}
```

```java
// 다음과 같이 변경하자
public final class ThreadLocal<T> {
    public ThreadLocal();
    public void set(T value);
    public T get();
}
```
