## 규칙 47. 어떤 라이브러리가 있는지 파악하고, 적절히 활용하라

표준 라이브러리(standard library)를 사용하면 그 라이브러리를 개발한 전문가의 지식뿐만 아니라 여러분보다 먼저 그 라이브러리를 사용한 사람들의 경험을 활용할 수 있다. 실제로 하려는 일과 큰 관련성도 없는 문제에 대한 해결 방법을 임의로 구현하느라 시간을 낭비하지 않아도 된다는 것이다.

또한 별다른 노력을 하지 않아도 그 성능이 점차로 개선되며, 시간이 흐르면 새로운 기능들도 추가된다. 

아울러 표준 라이브러리를 사용하면 주류(mainstream) 개발자들과 같은 코드를 만들게 된다. 그런 코드는 가독성이 높고, 유지보수가 쉬우며, 다른 개발자들이 재사용하기도 좋다.

중요한 새 릴리스(new major releas)가 나올 때마다 많은 기능이 새로 추가되는데, 그때마다 어떤 것들이 추가되었는지를 알아두는 것이 좋다. 자바 프로그래머라면 java.lang, java.util 안에 있는 내용은 잘 알고 있어야 하며, java.io의 내용도 어느 정도 알고 있어야 한다.

때로 라이브러리가 제공하는 기능이 만족스럽지 않을 때가 있다. 원하는 기능이 구체적일수록 그런 일이 자주 생긴다. 라이브러리에 담긴 기능을 우선적으로 고려해야 한다는 것은 맞지만, 그것이 원하는 기능을 구현하기에 충분치 않다면 대안을 찾아봐야 한다.

**요약하자면, 바퀴를 다시 발명하지 말라는 것이다.**