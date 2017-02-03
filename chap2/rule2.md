## 규칙2. 생성자 인자가 많을 때는 Builder 패턴 적용을 고려하라.
정적 팩터리나 생성자는 선택적 인자가 많은 상황에서 잘 적응하지 못한다.

### 1. 점층적 생성자 패턴(telescoping constructor pattern)
보통 프로그래머들은 이런 상황에 점층적 생성자 패턴을 적용한다.
```JAVA
public class NutritionFacts {
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  
  public NutritionFacts(int servingSize, int servings) {
    this(servingSize, servings, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories) {
    this(servingSize, servings, calories, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
  }
}
```
- 인자 수가 늘어나면 클라이언트 코드를 작성하기 어려워지고, 무엇보다 읽기 어려운 코드가 되고 만다.

### 2. 자바빈(JavaBeans) 패턴
두 번째 대안은 자바빈 패턴이다.
인자 없는 생성자를 호출하여 객체부터 만든 다음, 설정 메서드(setter methods)들을 호출하여 필수 필드뿐 안라 선택적 필드의 값들까지 채운다.

```JAVA
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCaloires(100);
```

- 1회의 함수 호출로 객체 생성을 끝낼 수 없으므로, 객체 일관성(consistency)이 일시적으로 깨질 수 있다.
- 자바빈 패턴으로는 변경 불가능(immutable) 클래스를 만들 수 없다.
