## 규칙20. 태그 달린 클래스 대신 클래스 계층을 활용하라

때로 두 가지 이상의 기능을 가지고 있으며, 그중 어떤 기능을 제공하는지 표시하는 태그(tag)가 달린 클래스를 만날 때가 있다.

```java
class Figure {
  enum Shape { RECTANGLE, CIRCLE };

  // 어떤 모양인지 나타내는 태그 필드
  final Shape shape;

  // 태그가 RECTANGLE일 때만 사용되는 필드
  double length;
  double width;

  // 태그가 CIRCLE일 때만 사용되는 필드
  double radius;

  // 원을 만드는 생성자
  Figure(double radius) {
    shape = Shape.CIRCLE;
    this.radius = radius;
  }

  // 사각형을 만드는 생성자
  Figure(double length, double width) {
    shape = Shape.RECTANGLE;
    this.length = length;
    this.width = width;
  }

  double area() {
    switch(shape) {
      case RECTANGLE:
        return length * width;
      case CIRCLE:
        return Math.PI * (radius * radius)
      default:
        throw new AssertionError();
    }
  }
}
```

태그 달린 클래스는 enum 선언, 태그 필드, switch 문 등의 상투적 코드가 반복되며 생성자에서 관련 없는 필드를 초기화하지 않는 한, 필드들을 final로 선언할 수도 없으므로 상투적인 코드(boilerplate)는 더 늘어난다.

태그 기반 클래스(tagged class)는 너저분한데다 오류 발생 가능성이 높고, 효율적이지도 않는다. 태그 기반 클래스는 클래스 계층을 얼기설기 흉내 낸 것일 뿐이다.

```java
abstract class Figure {
  abstract double area();
}

class Circle extends Figure {
  final double radius;

  Circle(double radius) { ... }

  double area() { ... }
}

class Rectangle extends Figure {
  final double length;
  final double width;

  Rectangle(double length, double width) {
    ...
  }

  double area() { ... }
}
```

클래스 계층으로 변환한 결과, 단순하고 명료하며 원래 클래스에 있던 난잡하고 상투적인 코드도 없다.

또한 자료형 간의 자연스로운 계층 관계를 반영할 수 있어서 유연성이 높아지고 컴파일 시에 형 검사(type checking)를 하기 용이하다.

### 요약

- 태그 기반 클래스 사용은 피해야 한다
- 태그 필드가 있는 클래스를 만나게 되면, 리팩터링을 통해 클래스 계층으로 변환할 방법이 없는지 고민
