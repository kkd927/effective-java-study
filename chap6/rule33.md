/* Created by choijaesun1 */
## Rule 33. ordinal을 배열 첨자로 사용하는 대신 EnumMap을 이용하라

#### ordinal 메서드가 반환하는 값을 배열 첨자로 이용하는 예

```JAVA
class Herb {
  enum Type {ANNUAL, PERENNIAL, BIENNIAL}

  final String name;
  final Type type;

  Herb(String name, Type type) {
    this.name = name;
    this.type = type;
  }

  @Override public String toString() {
    return name;
  }
}

// 화단에 심은 허브들을 나타내는 배열이 있고 이 허브들을 품종별로(일년생, 다년생, 격년생) 나눠야한다고 가정.

// 이렇게 사용하지 마라!
Herb[] garden = ...;

Set<Herb>[] herbsByType = //Indexed by Herb.Type.ordinal()
  (Set<Herb>[]) new Set[Herb.Type.values.length];

for(int i=0; i<herbsByType.length; i++) {
  herbsByType[i] = new HashSet<Herb>;
}

for(Herb h : garden) {
  herbsByType[h.type.ordinal()].add(h);
}

//결과 출력
for(int i=0;i < herbsByType.length; i++) {
  System.out.printf("%s : %s%n", Herb.Type.values()[i], herbsByType[i]);
}
```
  - 형 안전성을 보장하지 않는다.
  - 배열은 제네릭과 호환되지 않으므로 무점검 형변환이 일어남.

**enum 상수를 어떤 값에 대응시키려 한다면 EnumMap을 사용하자!**

```JAVA
//EnumMap을 사용해 enum 상수별 데이터를 저장하는 프로그램
Map<Herb.Type, Set<Herb>> herbsByType = new EnumMap<Herb.Type, Set<Herb>>(Herb.Type.class);

for(Herb.Type t : Herb.Type.values) {
  herbsByType.put(t, new HashSet<Herb>());
}
for(Herb h : garden) {
  herbsByType.get(h.type).add(h);
}
System.out.println(herbsByType);
```
  - EnumMap의 생성자는 키 자료형을 나타내는 class객체를 인자로 받는다. -> 한정적 자료형 정보 (Rule.29)

#### 잘못된 예2)

```JAVA
// ordinal() 값을 배열의 배열 첨자로 사용 - 이러시면 안됩니다.

public enum Phase { SOLID, LIQUID, GAS;
  public enum Transition {
    MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;

    //아래 배열의 행은 상전이 이전 상태를 나타내는 enum 상수의 ordinal 값을
    //첨자로 사용하고, 열은 상전이 이후 상태를 나타내는 enum 상수의
    //ordinal 값을 첨자로 사용한다.
    private static final Transition [][] TRANSITIONS = { // 상전이 테이블
      {null, MELT, SUBLIME},
      {FREEZE, null, BOIL},
      {DEPOSIT, CONDENSE, null}
    };

    //특정 상전이 과정을 표현하는 enum 상수를 반환
    public static Transition from(Phase src, Phase dst) {
      return TRANSITIONS[src.ordinal()][dst.ordinal()];
    }
  }
}
```
- 값이 수정되었을 때 안전하지 않다.
- EnumMap 으로 바꾼 코드

```JAVA
// EnumMap을 중첩해서 enum 쌍에 대응되는 데이터를 저장한다.
public enum Phase {
  SOLID, LIQUID, GAS;
   public enum Transition {
     MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID),
     BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
     SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID);

     private final Phase src;
     private final Phase dst;

     Transition(Phase src, Phase dst) {
       this.src = src;
       this.dst = dst;
     }

     // 상전이 맵 초기화
     private static final Map<Phase, Map<Phase, Transition>> m = new EnumMap<Phase, Map<Phase, Transition>>(Phase.class);

     static {
       for(Phase p : Phase.values()) {
         m.put(p, new EnumMap<Phase, Transition>(Phase.class));
       }
       for(Transition trans : Transition.values()) {
         m.get(trans.src).put(trans.dst, trans);
       }
     }

     public static Transition from(Phase src, Phase dst) {
       return m.get(src).get(dst);
     }
   }
}
```
  - 이걸 배열로 하면 나중에 enum 상수가 추가되었을 때, 배열을 변경해야한다. -> 실수하면 프로그램 안돌아감!

### 요약
ordinal 값을 배열 첨자로 사용하는 것은 적절하지 않으며, 대신 **EnumMap** 을 써라
