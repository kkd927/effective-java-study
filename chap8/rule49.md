## 규칙49. 객체화된 기본 자료형 대신 기본 자료형을 이용하라

모든 기본 자료형에는 대응되는 참조 자료형이 있는데, 이를 **객체화된 기본 자료형(boxed primitive type)** 이라 부른다. int, double, boolean의 객체화된 기본 자료형은 각각 Integer, Double, Boolean이다.

릴리스 1.5부터 자동 객체화(autoboxing)와 지동 비객체화(auto-unboxing)가 언어의 일부가 되었다. 이 기능들은 기본 자료형과 그 객체 표현형 간의 차이를 희미하게 만든다.

그런데 그 둘 사이에는 실질적인 차이가 있으므로, 둘 가운데 무엇을 사용하고 있는지를 아는 것이 중요하며, 어떤 것을 사용할지 신중하게 결정해야 한다.

기본 자료형과 객체화된 기본 자료형 사이에는 세 가지 큰 차이점이 있다.

**기본 자료형은 값만 가지지만 객체화된 기본 자료형은 값 외에도 신원(identity)을 가진다는 것이다.** 따라서 객체화된 기본 자료형 객체가 두 개 있을 때, 그 값은 같더라도 신원은 다를 수 있다.

**기본 자료형에 저장되는 값은 전부 기능적으로 완전한 값(fully functional value)이지만, 객체화된 기본 자료형에 저장되는 값에는 그 이외에 아무 기능도 없는 값, 즉 null이 하나 있다는 것이다.**

**기본 자료형은 시간이나 공간 요구량 측면에서 일반적으로 객체 표현형보다 효율적이라는 것이다.**

주의하지 않으면 이런 차이 때문에 곤란을 겪게 될 것이다. 관련 예제들을 살펴보자.

```java
Comparator<Integer> naturalOrder = new Comparator<Integer>() {
    public int compare(Integer first, Integer second) {
        return first < second ? -1 : (first == second ? 0 : 1);
    }
}

naturalOrder.compare(new Integer(42), new Integer(43)); // 1
```

위 예에서, 두 Integer 객체는 42라는 동일한 값을 나타내므로, 반환 값은 0이 되어야 하지만 실제 반환되는 값은 1이다. 연산자 ==는 객체 참조를 통해 두 객체의 신원을 비교하기 때문이다. **객체화된 기본 자료형에 == 연산자를 사용하는 것은 거의 항상 오류라고 봐야한다.**

```java
Comparator<Integer> naturalOrder = new Comparator<Integer>() {
    public int compare(Integer first, Integer second) {
        int f = first; // Auto-unboxing
        int s = second; // Auto-unboxing
        return first < second ? -1 : (first == second ? 0 : 1); // No unboxing
    }
}
```

위와 같이 지역 int 변수를 통해 문제가 되는 신원 비교를 피할 수 있다.

```java
public class Unbelievable {
    static Integer i;

    public static void main(String[] args) {
        if (i == 42) {
            System.out.println("Unbelievable");
        }
    }
}
```

위 프로그램은 Unbelievable을 출력하지 않고 NullPointerException을 발생시킨다. 거의 모든 경우에, 기본 자료형과 객체화된 기본 자료형을 한 연산 안에 엮어 놓으면 객체화된 기본 자료형은 자동으로 기본 자료형으로 변환한다. null인 객체 참조를 기본 자료형으로 변환하려 시도하면 NullPointerException이 발생한다.

```java
public static void main(String[] args) {
    Long sum = 0L;
    for (long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
    }
    System.out.println(sum);
}
```

위 프로그램은 예상보다 훨씬 더 느리다. 오류나 경고 없이 컴파일되는 프로그램이지만 변수가 계속해서 객체화와 비객체화를 반복하기 때문에 성능이 느려진다.

**그렇다면 객체화된 기본 자료형은 언제 사용해야 하나?** 컬렌션의 요소, 키 값으로 사용할 때다. 컬렉션에는 기본 자료형을 넣을 수 없으므로 객체화된 자료형을 써야 한다. 또한 형인자 자료형의 형인자로는 객체화된 기본 자료형을 써야한다는 일반 규칙의 특수한 형태다.
