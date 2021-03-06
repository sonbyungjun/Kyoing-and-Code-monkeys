# 라이브러리를 익히고 사용하라

## 핵심 요약

### 흔한 실수

* 흔하지만 문제가 심각한 코드

  ```java
  static Random rnd = new Random();

  static int random(int n) {
    return Math.abs(rnd.nextInt()) % n;
  }
  ```

* 문제점

  1. n이 그리 크지 않은 2의 제곱수라면 얼마 지나지 않아 같은 수열이 반복 됨.

  1. n이 2의 제곱수가 아니라면 몇몇 숫자가 평균적으로 더 자주 반환 됨.

  1. 지정한 범위 **바깥**의 수가 종종 튀어나올 수 있음.

      > Math.bas를 이용해 음수가 아닌 정수로 매핑하기 때문임.
      >
      > 해결을 위해서는 의사난수 생성기, 정수론, 2의 보수 계산 등에 조예가 깊어야 함.  
      > 하지만 우리는 직접 해결 할 필요는 없음. 라이브러리 이용하면 됨.

      * 자바 7 이전 - `Random.nextInt()` 사용하면 됨.

      * 자바 7 이후 - `ThreadLocalRandom`으로 대체

* 표준 라이브러리 장점

  1. 표준 라이브러리를 사용하면 그 코드를 작성한 전문가의 지식과 우리보다 앞서 사용한 다른 프로그래머 들의 경험을 활용 할 수 있다.

  1. 핵심적인 일과 크게 관련 없는 문제를 해결하느라 시간을 허비 않아도 됨.

  1. 따로 노력하지 않아도 성능이 지속해서 개선된다는 점.

  1. 기능이 점점 많아진다는 것.

  1. 우리가 작성한 코드가 많은 사람에게 낯익은 코드가 된다는 것.

  ## 핵심 정리

바퀴를 다시 발명하지 말자. 아주 특별한 나만의 기능이 아니라면 누군가 이미 라이브러리 형태로 구현해놓았을 
가능성이 크다. 열심히 찾아보자. 일반적으로 라이브러리 코드는 직접 작서한 것보다 품질이 좋고, 점차 개선될 가능성이
크다. 라이브러리 코드는 개발자 각자가 작성하는 것보다 주목을 훨씬 많이 받으므로 코드 품질도 그만큼 높아진다.