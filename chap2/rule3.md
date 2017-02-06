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

#### 위 방법들은 AccessibleObject.setAccessible 메서드의 도움을 받아 권한을 획득한 클라이언트는 리플렉션(reflection) 기능을 통해 private 생성자를 호출할 수 있다.

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
