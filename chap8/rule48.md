## 규칙48. 정확한 답이 필요하다면 float와 double은 피하라

float와 double은 기본적으로 과학 또는 엔지니어링 관련 계산에 쓰일 목적으로 설계된 자료형이다. 

이 자료형들은 *이진 부동 소수점 연산(binary floating-point arithmetic)* 을 수행하는데, 이것은 넓은 범위의 값(magnitude)에 대해 정확도가 높은 근사치를 제공할 수 있도록 세심하게 설계된 연산이다. 하지만 정확한 결과를 제공하지는 않기 때문에 정확한 결과가 필요한 곳에는 사용하면 안 된다. 

**float와 double은 특히 돈과 관계된 계산에는 적합하지 않다.** 돈 계산을 할 때는 BigDecimal, int 또는 long을 사용한다는 원칙을 지켜야 한다.

하지만 BigDecimal을 쓰는 방법에는 두 가지 문제가 있다. 기본 산술연산 자료형(primitive arithmetic type)보다 사용이 불편하며, 느리다. 대안으로 int나 long을 사용할 수 있다.

**관계된 수치들이 신진수 아홉 개 이하로 표현이 가능할 때는 int를 쓰라. 18개 이하로 표현 가능할 때는 long을 쓰라. 그 이상일 때는 BigDecimal을 써야 한다.**
