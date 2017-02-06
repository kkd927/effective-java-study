## 규칙3. private 생성자나 enum 자료형은 싱글턴 패턴을 따르도록 설계하라

JDK 1.5 이전에는 싱글턴을 구현하는 방법이 두 가지 였다.

### 1. 정적 멤버는 final로 선언

```JAVA
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  
  public void leaveTheBuilding() { ... }
}
```

- private 생성자는 pulic static final 필드인 Elivs.INSTANCE를 초기화 할 때 한 번만 호출된다.

### 2. public으로 선언된 정적 팩터리 

```JAVA
public class Elvis {
  private static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  public static Elvis getInstance() { return INSTANCE; }
  
  public void leaveTheBuilding() { ... }
}
```

- API를 변경하지 않고도 싱글턴 패턴을 포기할 수 있다.
- 제네릭 타입을 수용하기 쉽다.

### 위 방법들의 단점
- AccessibleObject.setAccessible 메서드의 도움을 받아 권한을 획득한 클라이언트는 리플렉션(reflection) 기능을 통해 private 생성자를 호출할 수 있다.

```JAVA
import java.lang.reflect.Constructor;

public class PrivateInvoker {
  public static void main(String[] args) throws Exception {
    // 리플렉션과 setAccessible 메서드를 통해 private로 
    // 생성자의 호출 권한을 획득한다.
    Constructor<?> con = Private.class.getDeclaredConstructors()[0];
    con.setAccessible(true);
    Private p = (Private) con.newInstance();
  }
}

class Private {
  private Private() { ... }
}
```

- 직렬화 기능(Serializable) 클래스로 만들려면 클래스 선언에 implements Serializable을 추가하는 것으로 부족.
- 모든 필드를 transient로 선언하고 readResolve 메서드를 추가. ([참고](http://hyeonstorage.tistory.com/254)) <br/>(그렇지 않으면 serialize된 객체가 역직렬화(deserialize)될 때마다 새로운 객체가 생기게 된다.)

```JAVA
// 싱글턴 상태를 유지하기 위한 readResolve 구현
private Object readResolve() {
  // 동일한 Elvis 객체가 반환되도록 하는 동시에, 가짜 Elivs 객체는 GC가 처리하도록 만든다.
  return INSTANCE;
}
```

### 3. 원소가 하나뿐인 enum 자료형을 이용.

```JAVA
public enum Evlis {
  INSTANCE;
  
  public void leaveTheBuilding() { ... }
}
```

- 기능적으로는 public 필드를 사용하는 구현법과 동등.
- 좀 더 간결하고 직렬화가 자동으로 처리된다.
- 리플렉션(reflection)을 통한 공격에도 안전하다.

### 요약
원소가 하나뿐인 enum 자료형이야말로 싱글턴을 구현하는 가장 좋은 방법이다.
