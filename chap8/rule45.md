## 규칙45. 지역 변수의 유효범위를 최소화하라

지역 변수의 유효범위를 최소화하면 가독성(readability)과 유지보수성(maintainability)이 좋아지고, 오류 발생 가능성도 줄어든다.

**지역 변수의 유효범위를 최소화하는 가장 강력한 기법은, 처음으로 사용하는 곳에서 선언하는 것이다.** 사용하기 전에 선언하면 프로그램의 의도를 알고자 소스 코드를 읽는 사람만 혼란스럽게 할 뿐이다.

**거의 모든 지역 변수 선언에는 초기값(initializer)이 포함되어야 한다.** 변수를 적절히 초기화하기에 충분한 정보가 없다면, 그때까지는 선언을 미뤄야 한다.

지역 변수의 유효범위를 최소화하는 숙어를 하나 살펴보면,

```java
for (int i = 0, n = expensiveComputation(); i < n; i++) {
    doSomething(i);
}
```

두 번째 변수 n은 i 값의 범위를 제한하는 용도로 쓰이고 있는데, 그 값을 계산하는 비용이 꽤 크다면, 미리 계산해 두고 사용하면 매번 재계산할 필요가 없다.

지역 변수의 유효범위를 최소화하는 마지막 전략은 메서드의 크기를 줄이고 특정한 기능에 집중하라는 것이다. 두 가지 서로 다른 기능을 한 메서드 안에 넣어두면 한 가지 기능을 수행하는데 필요한 지역 변수의 유효범위가 다른 기능까지 확장되는 문제가 생길 수 있다.