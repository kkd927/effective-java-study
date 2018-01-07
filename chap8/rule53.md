## 규칙53. 리플렉션 대신 인터페이스를 이용하라

java.lang.reflect의 핵심 리플렉션 기능(core reflection facility)을 이용하면 메모리에 적재된 클래스의 정보를 가져오는 프로그램을 작성할 수 있다. 하지만 이런 능력에는 대가가 따른다.

- 컴파일 시점에 자료형을 검사함으로써 얻을 수 있는 이점들을 포기해야 한다(exception checking 포함). 리플렉션을 통해 존재하지 않는, 또는 접근할 수 없는 메서드를 호출하면 실행 도중에 오류가 발생할 것이다. 그러니 특별히 주의해야 한다.

- 리플레션 기능을 이용하는 코드는 보기 싫은데다 장황하다. 영리한 코드와는 거리가 멀고, 가독성도 떨어진다.

- 성능이 낮다. 리플렉션을 통한 메서드 호출 성능은, 일반적인 메서드 호출에 비해 훨씬 낮다. 얼마나 낮은지 정확히 말하기는 어렵다. 고려해야할 조건들이 다양하기 때문.

핵심 리플렉션 기능은 원래 컴포넌트 기반 응용프로그램 저작 도구(component-based application builder tool)를 위해 설계된 기능이었다. 그런 도구는 보통 요청에 따라 클래스를 메모리에 올린 다음, 리플렉션 기능을 통해 어떤 메서드와 생성자가 지원되는지를 알아낸다.

**명심할 것은, 일반적인 프로그램은 프로그램 실행 중에 리플렉션을 통해 객체를 이용하려 하면 안된다는 것이다. 하지만 리플렉션을 아주 제한적으로만 사용하면 오버헤드는 피하면서 리플렉션의 다양한 장점을 누릴 수 있다.**

컴파일 시점에는 존재하지 않는 클래스를 이용해야 하는 프로그램 가운데 상당수는 해당 클래스 객체를 참조하는데 사용할 수 있는 인터페이스나 상위 클래스(규칙 52)를 컴파일 시점에 이미 갖추고 있는 경우가 많다. 그럴 때는, **객체 생성은 리플렉션으로 하고 객체 참조는 인터페이스나 상위 클래스를 통하면 된다.**

```java
// 객체 생성은 리플렉션으로, 참조와 사용은 인터페이스로
public static void main(String[] args) {
    // 클래스 이름을 Class 객체로 변환
    Class<?> cl = null;

    try {
        cl = Class.forName(args[0]);
    } catch(ClassNotFoundException e) {
        System.err.println("Class not found.");
        System.exit(1);
    }

    // 해당 클래스의 객체 생성
    Set<String> s = null;

    try {
        s = (Set<String>) cl.newInstance();
    } catch(IllegalAccessException e) {
        System.err.println("Class not accessible.");
        System.exit(1);
    } catch(InstantiationException e) {
        System.err.println("Class not instantiable.");
        System.exit(1);
    }

    // Set 이용
    s.addAll(Arrays.asList(args).subList(1, args.length));
    System.out.println(s);
}
```

장난감에 불과한 프로그램이지만, 사용한 기술은 아주 강력하다. 이 프로그램은 하나 이상의 객체를 공격적으로 조작하여 해당 구현이 Set의 일반 규약을 준수하는지 검증하는 일반적 집합 검사 도구(generic set tester)로 쉽게 변경될 수 있다.

한편 이 예제는 리플렉션의 두 가지 단점도 보여준다.

첫번째로 이 예제는 세가지 실행시점 오류(runtime error)를 발생시키는데, 리플렉션으로 객체를 만들지 않았더라면 컴파일 시점에 검사할 수 있는 오류들이다.

두번째로 이 예제는 이름에 대응하는 클래스의 객체를 생성하기 위해 스무줄 가량의 멍청한 코드를 사용하고 있는데, 생성자를 호출로 대신했으면 한 줄이면 되었을 코드다.

하지만 이런 문제는 객체를 만드는 부분에섬나 나타난다. 객체가 만들어지고 나면 다른 Set 객체와 분간할 수 없다.

### 요약

리플렉션은 특정한 종류의 복잡한 시스템 프로그래밍에 필요한 강력한 도구다. 하지만 단점이 많다. 컴파일 시점에는 알 수 없는 클래스를 이용하는 프로그램을 작성하고 있다면, 리플렉션을 사용하되 가능하면 객체를 만들 때만 사용하고, 객체를 참조할 때는 컴파일 시 알고 있는 인터페이스나 상위 클래스를 이용하라.