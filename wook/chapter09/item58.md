# 전통적인 for 문보다는 for-each 문을 사용하라

## 핵심 요약

* 전통적인 `for` 문으로 컬렉션 순회

  ```java
  for (Iterator<Element> i = c.iterator(); i.hasNext();)  {
    Element e = i.next();
    ... // e로 무언가를 한다.
  }
  ```

* 전통적인 `for` 문으로 배열을 순회

  ```java
  for (int i = 0; i < a.length; i++>) {
    ... // a[i]로 무언가를 한다.
  }
  ```

  > `while` 문 보다는 낫지만 가장 좋은방법은 아니다.  

 ### 이유 
 
 1. 반복자와 인덱스 변수는 코드를 지저분하게 할 뿐이고 우리에게 필요한건 원소들 뿐.
 
 1. 혹시라도 잘못된 변수를 사용 했을 때 컴파일러가 잡아주리라는 보장이 없음.
 
 1. 컬렉션이냐 배열이냐에 따라서 코드 형태가 달라짐.

### 해결 방법

for-each 문을 사용하면 모두 해결. 참고로 정식 이름은 `향상된 for 문 (enhanced for statement)`이다.

* 컬렉션과 배열을 순회하는 올바른 관용구

  ```java
  for (Element e : elements) {
    ... // e로 무언가를 한다.
  }
  ```

### 반복문 중첩시 저지르기 쉬운 실수

* 버그가 있는 중첩 반복문

  ```java
  enum Suit {CLUB, DIAMOND, HEART, SPADE}
  enum Rank {ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT,
             NINE, TEN, JACK, QUEEN, KING}

  ...

  static Collection<Suit> suits = Arrays.asList(Suit.values());
  static Collection<Rank> ranks = Arrays.asList(Rank.values());

  List<Card> deck = new ArrayList<>();
  for (Iterator<Suit> i = suits.iterator(); i.hasNext();) {
    for(Iterator<Rank> j = ranks.iterator(); j.hasNext();) {
      deck.add(new Card(i.next(), j.next()));
    }
  }
  ```

  > `ranks`의 반복문에서 `i.next()` 를 하기 때문에 `suits`내의 원소가 바닥이 나게 되면 `NoSuchElementException`을 던지게 됨.

* 같은 버그, 다른 증상!

  ```java
  enum Face {ONE, TWO, THREE, FOUR, FIVE, SIX}

  ...

  Collection<Face> faces = EnumSet.allOf(Face.class);

  for(Iterator<Face> i = faces.iterator(); i.hasNext();) {
    for(Iterator<Face> j = faces.iterator(); j.hasNext();) {
      System.out.println(i.next() + " " + j.next());
    }
  }
  ```

  > 두 컬렉션의 크기가 같기 때문에 예외를 던지지 않지만 36번 순회하며 모든 경우의 수에대해서 출력해주지 않고 ONE ONE, TWO TWO ... SIX SIX 까지 6번 출력하고 종료.

* 문제는 해결 하지만 보기 좋진 않음.

  ```java
  for (Iterator<Suit> i = suits.iterator(); i.hasNext();) {
    Suit suit = i.next();
    for(Iterator<Rank> j = ranks.iterator(); j.hasNext();) {
      deck.add(new Card(suit, j.next()));
    }
  }
  ```

* for-each 문으로 작성

  ```java
  for (Suit suit : suits) {
    for(Rank rank : ranks) {
      deck.add(new Card(suit, rank));
    }
  }
  ```

### for-each 문을 사용할 수 없는 상황 세가지

* 파괴적인 필터링

  > 컬렉션을 순회하면서 선택된 원소를 제거해야 한다면 반복자의 remove 메서드를 호출해야 함. 자바 8부터는 Collection의 removeIf 메서드를 사용해 컬렉션을 명시적으로 순회하는 일을 피할 수 있음.

* 변형

  > 리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 반복자나 배열의 인덱스를 사용해야 함.

* 병렬 반복

  > 여러 컬렉션ㅇ르 병렬로 순회해야 한다면 각각의 반복자와 인덱스 변수를 사용해서 엄격하고 명시적으로 제어해야 함.

## 핵심 정리

전통적인 `for` 문과 비교했을 때 `for-each` 문은 명료하고, 유연하고, 버그를 예방해줌. 서능 저하도 없다. 가능한 모든 곳에서 `for` 문이 아닌 `for-each` 문을 사용하자.