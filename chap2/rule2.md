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

### 3. 빌더(Builder) 패턴

점층적 생성자 패턴의 안정성에 자바빈 패턴의 가독성을 결합한 세 번째 대안.

```JAVA
public class NutritionFacts {
	private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;

	public static class Builder {
		// 필수 인자
		private final int servingSize;
		private final int servings;
		// 선택 인자
		private int calories = 0;
		private int fat = 0;

		public Builder(int servingSize, int servings) {
			this.servingSize = servingSize;
			this.servings = servings;
		}

		public Bulder calories(int val) { calories = val; return this; }
		public Builder fat(int val) { fat = val; return this; }

		public NutritionFacts build() {
			return new NutritionFacts(this);
		}
	}

	private NutritionFacts(Builder builder) {
		servings = builder.servingSize;
		servings = builder.servings;
		calories = builder.calories;
		fat = builder.fat;
	}
}
```

- 객체가 변경 불가능, 모든 인자의 기본 값이 한곳에 모여있다.
- 인자에 [불변식(invariant)](http://kevinx64.net/198)을 적용 가능하다.
build 메서드 안에서 해당 불변식이 위반되었는지 검사할 수 있다.

```JAVA
/**
 *  @pre    calories >= 0
 *  @post   getCalories() == val
 */
public Builder setName(int val){
	calories = val;
	return this;
}

public NutritionFacts build() {
	if (calories < 0) {
		throw new IllegalStateException();
	}
	return new NutritionFacts(this);
}
```

- 빌더 객체는 여러 개의 varargs 인자를 받을 수 있다.
- 객체를 생성하려면 우선 빌더 객체를 생성해야되므로 성능이 중요한 상황에선 오버헤드가 문제.

### 요약
빌더 패턴은 인자가 많은 생성자나 정적 팩터리가 필요한 클래스를 설계할 때, 특히 대부분의 인자가 선택적 인자인 상황에 유용
