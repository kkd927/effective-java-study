## 규칙5. 불필요한 객체는 만들지 말라

기능적으로 동일한 객체는 필요할 때마다 만드는 것보다 재사용하는 편이 낫다.

### 1. 변경 불가능(immutable) 객체는 언제나 재사용할 수 있다.

```java
String s = new String("stringette");
String s = "stringette"; // 바람직
```

- 순환문이나 자주 호출되는 메서드 안에 있을 경우 차이.
- 아래의 경우 가상 머신(virtual machine)에서 실행되는 모든 코드가 해당 String 객체를 재사용.

```java
new Boolean("true");
Boolean.valueOf("true"); // 대체로 더 바람직
```

- 생성자와 정적 팩터리 메서드를 함께 제공하는 변경 불가능 클래스의 경우 <br>생성자 대신 정적 팩터리 메서드를 이용하면 불필요한 객체 생성을 피할 수 있을 때가 많다.

### 2. 변경할 일이 없다면 변경 가능한 객체도 재사용 가능.

```java
public class Person {
  private final Date birthDate;

  public boolean isBabyBoomer() {
    Calendar gmtCal = 
      Calendar.getInstance(TimeZone.getTimeZone("GMT"));
      
    gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomStart = gmtCal.getTime();

    gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomEnd = gmtCal.getTime();

    return birthDate.compareTo(boomStart) >= 0 &&
      birthDate.compareTo(boomEnd) < 0;
  }
}
```

- 위 코드는 메서드 호출마다 Calendar 객체 하나, TimeZone 객체 하나, Date 객체 두 개를 쓸데없이 만든다.

```java
public class Person {
  private final Date birthDate;

  /**
   * 베이비 붐 시대의 시작과 끝
   */
   private static final Date BOOM_START;
   private static final Date BOOM_END;

   static {
     Calendar gmtCal = 
       Calendar.getInstance(TimeZone.getTimeZone("GMT"));
       
     gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
     BOOM_START = gmtCal.getTime();
 
     gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
     BOOM_END = gmtCal.getTime();
   }

  public boolean isBabyBoomer() {
    return birthDate.compareTo(BOOM_START) >= 0 &&
      birthDate.compareTo(BOOM_END) < 0;
  }
}
```

- 정적 초기화 블록(static initializer)을 통해 개선.
- boomStart와 boomEnd를 static final 필드로 변경하여 상수임을 분명히 함.
<br>
- Person 클래스가 초기화도니 다음 isBabyBoomer 메서드가 한 번도 호출되지 않는다면, 쓸데없는 초기화이다.
- 초기화 지연(lazy initialization) 기법을 사용하면 피할 수 있지만, 구현이 복잡해질 뿐더러 앞서 달성한 것 이상으로 성능을 개선하기도 어렵기 때문에 추천하지 않는다.


### 3. 자동 객체화(autoboxing)을 유의해라.

```java
public static void main(String[] args) {
  Long sum = 0L; // long이 아니라 Long으로 선언됨.
  for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
  }
  System.out.println(sum);
}
```

- Long sum이 더해질 때 마다 Long 객체가 생성된다.
- 객체 표현형 대신 기본 자료형을 사용하고, 생각지도 못한 자동 객체화가 발생하지 않도록 유의.
- 하지만 코드의 명확성과 단순성을 높이고 프로그램의 능력을 향상시킬 수 있다면, 일반적으로는 만드는 것이 좋다.

### 4. 직접 관리하는 객체 풀(object pool)을 만들어 객체 생성을 피하는 기법
- 데이터베이스 연결 같은 객체 생성 비용이 극단적으로 높지 않다면 사용하지 않는 것이 좋다.
- 최신 JVM은 고도로 최적화된 쓰레기 수집기를 갖고 있어서, 가벼운(lightweight) 객체라면 객체 풀보다 월등한 성능을 보여준다.


### 5. 방어적 복사(defensive copy)
- 방어적 복사가 요구되는 상황에서 객체를 재사용하는 데 드는 비용은 쓸데없이 같은 객체를 여러 벌 만드는 비용보다 훨씬 높다.
- 골치 아픈 버그나 보안 결함이 생길 수 도 있으니 방어적 복사본에는 재사용하지 말라.


