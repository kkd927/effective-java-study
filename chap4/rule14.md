## 규칙14. public 클래스 안에는 public 필드를 두지말고 접근자 메서드를 사용하라

```java
// 이런 저급한 클래스는 절대 누구에게 보여주지 말 것
class Point {
  public double x;
  public double y;
}
```
이런 클래스는 데이터 필드를 직접 조작할 수 있어서 __캡슐화__의 이점을 누릴 수가 없다.

```java
class Point {
  private double x;
  private double y;

  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double getX() { return x; }
  public double getY() { return y; }

  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}
```

 - private 필드와 public 접근자 메서드(getter)로 바꿔야 마땅
 - 변경 가능 클래스라면 수정자(mutator) 메서드(setter)도 제공
 - package-private 클래스나 private 중첩 클래스는 데이터 필드를 공개하는 것이 바람직할 때도 있다.
 (클래스 정의로 보나 클라리언트 코드로 보나, 접근자 메서드보다는 시각적으로 깔끔해 보인다)
