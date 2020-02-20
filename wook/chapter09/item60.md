# 정확한 답이 필요하다면 float와 double은 피하라

## 핵심 요약

`float`와 `double` 타입은 과학과 공학 계산용으로 설계됨. 이진 부동소수점 연산에 쓰이며, 넓은 범위의 수를 빠르게 정밀한 **근사치**로 계산 하도록 세심하게 설계되었음.

> 따라서 정확한 결과가 필요할 때는 사용하면 안 된다. `float`와 `double` 타입은 특히 금융 관련 계산과는 맞지 않는다. 0.1 혹은 10의 음의 거듭제곱 수(10<sup>-1</sup>, 10<sup>-2</sup>등)를 표현할 수 없기 떄문이다.

* 1.03달러가 있는데 그중 42센트를 사용

  ```java
  System.out.println(1.03 - 0.42);
  ```

  0.6100000000000001을 출력함.

* 1달러가 있었는데 10센트짜리 사탕 9개를 삼

  ```java
  System.out.println(1.00 - 9 * 0.10);
  ```

  0.09999999999999998을 출력함.

  > 출력하기전에 반올림하면 된다 생각할지 모르지만, 반올림을 해도 틀린 답이 나올 수 있다. 주머니에 1달러가 있고, 선반에 10센트, 20센트, 30센트, ••• 1달러짜리 사탕이 있음. 10센트짜리부터 하나씩, 살 수 있을 때까지 사보자. 사탕을 몇 개나 살 수 있고, 잔돈은 얼마가 남을까?

* 어설픈 해답

  ```java
  public static void main(String[] sargs) {
    double funds = 1.00;
    int itemsBought = 0;
    for (double price = 0.10; funds >= price; price += 0.10) {
      funds -= price;
      itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러)" + funds);
  }
  ```

  사탕 3개를 구입한 후 잔돈은 0.3999999999999999달러가 남는다.

* BigDecimal을 사용한 해법

  ```java
  public static void main(String[] args) {
    final BigDecimal TEN_CENTS = new BigDecimal(".10");

    int itemsBought = 0;
    BigDecimal funds = new BigDecimal("1.00");
    for (BigDecimal price = TEN_CENTS; funds.compareTo(price) >= 0; price = price.add(TEN_CENTS)) {
      funds = funds.subtract(price);
      itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러)" + funds);
  }
  ```

    4개 구입한 후 잔돈 0달러 남음.

    * 단점

      1. 기본 타입보다 쓰기가 훨씬 불편.

      1. 기본 타입보다 훨씬 느림.
  
* 정수 타입을 이용한 해법 (BigDecimal 대안)

  ```java
  public static void main(String[] args) {
    int itemsBought = 0;
    int funds = 100;
    for (int price = 10; funds >= price; price += 10) {
      funds -= price;
      itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러)" + funds);
  }
  ```

## 핵심 정리

정확한 답이 필요한 계산에는 float이나 double을 피하라. 소수점 추적은 시템에게 맡겨라. 코딩 시의 불편함이나 성능 저하를 신경쓰지 않겠다면 BigDecimal을 사용하라. BigDecimal이 제공하는 여덟 가지 반올림 모드를 수행해야 하는 비즈니스 계산에서 아주 편리한 기능이다. 반면, 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면 int나 long을 사용하라. 숫자를 아홉 자리 십진수로 표한할 수 있다면 int를 사용하고, 열여덟 자리 십진수로 표현할 수 있다면 long을 사용하라. 열여덟 자리를 넘어가면 BigDecimal을 사용해야 한다.
