# 가변인수는 신중히 사용하라

## 핵심 요약

가변인수 메서드를 호출하면 ， 가장 먼저 인수의 개수와 길이가 같은 배열을 만들고 인수들을 이 배열 에 저장하여 가변인수 메서드에 건네 줌.

* 간단한 사용 예

    ```java
    stat ic int sum ( int ... a rgs) {
      int sum = 0;
      for (int arg : args) {
        sum += arg;
      }
      return sum;
    }
    ```
    
    > 인수가 1개 이사이어야 할 때도 있음. 예를 들어 최소값을 찾는 메서드인데 인수를 0개 받게 만드는 것은 좋지 않음.

* 잘못 구현한 예

    ```java
    static int min(int ... args) {
        if (args.length == 0)
          throw new IllegalArgumentException(" 인수가 1개 이상 필요합니다.");
    
        int min = args[0];
        for (int i = 1; i < args. length; i++) {
          if (args[i] < min) {
            min = args [i] ;
          }
        }
        return min;
    }
    ```

  * 문제

    1. 인수를 0개만 넣어 호출하면 컴파일이 아닌 런타임에 실패 한다는 점.

    1. 코드가 지저분함.

    1. args 유효성 검사를 명시적으로 해야 함.

    1. min 초기값을 `Integer.MAX_VALUE`로 설정하지 않고는 `foreach`문 사용 못함.

* 제대로 사용하는 방법

    ```java
    static int main(int firstArg, int... remainingArgs) {
      int min = firstArg;
      for (int arg : remainingArgs) {
        if (arg < min>) {
          min = arg;
        }
      }
      return min;
    }
    ```

    > 첫 번째로 평범한 매개변수를 받고, 가변인수를 두 번째로 받으면 깔끔

* 성능에 민감한 상황

    > 가변인수 메서드는 호출될 떄마다 배열을 하나 할당하고 초기화한다. 비용 감당은 안되지만, 가변인수의 유연성이 필요할 때 선택할 수 있는 패턴이 있음.

    1. 해당 패턴

        ```java
        public void foo() {}
        public void foo(int a1) {}
        public void foo(int a1, int a2) {}
        public void foo(int a1, int a2, int a3) {}
        public void foo(int a1, int a2, int a3, int... rest) {}
        ```

    1. EnumSet의 정적패터리도 이 기법을 사용해 열거 타입 집합 생성 비용을 최소화 함.

## 핵심 정리

  > 인수 개수가 일정하지 않은 메서드를 정의해야 하면 필수 매개변수를 가변인수 앞에 두고 가변일 수를 사용할 때는 성능 문제까지 고려하자.