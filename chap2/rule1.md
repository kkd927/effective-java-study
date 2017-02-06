## 규칙1. 생성자 대신 정적 팩터리 메서드를 사용할 수 없는지 생각해 보라

Boolean 클래스의 예
```JAVA
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```

#### 1. 생성자와 달리 정적 팩터리 메서드에는 이름이 있다.

  - 정적 팩터리는 이름을 잘 짓기만 한다면 사용하기도 쉽고, 클라이언트 코드의 가동성도 높아진다.
  - 같은 시그니처를 갖는 생성자를 여러 개 정의할 필요가 있을 때는 그 생성자들을 정적 팩터리 메서드로 바꾸고, 메서드 이름을 보면 차이가 명확히 드러나도록 작명

#### 2. 생성자와는 달리 호출할 때마다 새로운 객체를 생성할 필요가 없다.

  - 변경 불가능 클래스라면 이미 만들어 둔 객체를 활용할 수도 있고, 만든 객체를 캐시 해놓고 재사용할 수도 있다.
  - 동일한 객체가 요청되는 일이 잦고, 특히 객체를 만드는 비용이 클 때 적용하면 성능을 크게 개선할 수 있다.
  - 어떤 시점에 어떤 객체가 얼마나 존재할지를 정밀하게 제어할 수 있다.
  
#### 3. 생성자와는 달리 반환값 자료형의 하위 자료형 객체를 반환할 수 있다.

  - public 정적  팩터리 메서드가 반환하는 객체의 클래스가 public일 필요가 없다.
  - 메서드에 주어지는 인자를 이용하면 어떤 클래스의 객체를 만들지도 동적으로 결정할 수 있다.
  
```JAVA
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}
```

  - API를 호출하는 코드 쪽에서는 내부적으로 어떤 클래스가 이용되는지 알 수가 없다.
  - 성능, 상황 등에 맞게 내부적으로 코드를 수정 가능.
  - 클라이언트는 팩터리 메서드가 반환하는 객체의 실제 클래스를 알 수도 없고, 알 필요도 없다. <br/> 단지 EnumSet의 하위 클래스라는 사실만 중요할 뿐이다.
  
#### 4. public이나 protected로 선언된 생성자가 없으므로 하위 클래스를 만들 수 없다.

  - 계승(inheritance) 대신 구성(composition) 기법을 쓰도록 장려.

#### 5. 정적 팩터리 메서드가 다른 정적 메서드와 확연히 구분되지 않는다.

- 클래스나 인터페이스 주석을 통해 정적 팩터리 메서드임을 표시.
- 흔히 쓰이는 정적 팩터리 메서드 이름 사용 (valueOf, of, getInstance, newInstance, get*Type*, new*Type*).

```
valueOf: 인자로 주어진 값과 같은 값을 갖는 객체를 반환
of: valueOf를 더 간단하게 쓴 것
getInstance: 인자에 기술된 객체 반환, 싱글톤 패턴을 따를 경우, 인자 없이 항상 같은 객체 반환 
newInstance: getInstance와 같지만 호출할 때마다 다른 객체 반환
```
