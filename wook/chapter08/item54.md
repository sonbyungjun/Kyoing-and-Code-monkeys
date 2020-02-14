# null 이 아닌 ， 빈 컬렉션이나 배열을 반환하라

## 핵심 요약

* 따라 하지 말 것

    ```java
    private final List<Cheese> cheesesInStock = ...;
    
    public List<Cheese> getCheeses() {
      return CheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
    }
    ```
    
    > 이렇게 null을 반환하면, 클라이언트는 이 null 상황을 처리하는 코드를 추가로 작성해야 함.
    > ```java
    > if(cheeses ! = null &&& ...)
    > ```
    >
    > 이렇게 늘 방어 코드를 작성해줘야 함.

### 빈 컨테이너를 할당하는 것도 비용이 발생하니 null을 반환하는 쪽이 낫다는 주장

* 문제점

    1. 성능 분석을 해봤을 때 이러한 할당이 성능 저하의 주범이라고 확인되지 않는 한, 이정도의 성능 차이는 신경 쓸 수준이 아님.

    1. 빈 컬렉션과 배열은 굳이 새로 하당하지 않고도 반환할 수 있음.

* 빈 컬렉션을 반환하는 올바른 예

    ```java
    public List<Cheese> getCheeses() {
      return new ArrayList<>(cheesesInStock);
    }
    ```

    > 대부분 상황에서는 이렇게 하면 됨.

* 매번 새로 할당하지 않도록 함 (불변객체)

    ```java
    public List<Cheese> getCheeses() {
      return cheesesInStock.isEmpty() ? Collections.emptyList()
          : new ArrayList<>(cheesesInStock);
    }
    ```

    > 집합이 필요하면, `Collections.emptySet`  
    > 맵이 필요하면, `Collections.emptyMap`

* 배열을 반환하는 올바른 방법

    ```java
    public Cheese[] getCheeses() {
      return cheeseInStock.toArray(new Cheese[0]);
    }
    ```

    > 이 방식이 성능을 떨어뜨릴 것 같으면 길이 0짜리 배열을 미리 선언해두고 매번 그걸 반환하면 됨. 길이 0이 배열은 모두 불변이기 때문.

* 빈 배열을 매번 새로 할당하지 않도록 함

    ```java
    private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

    public Cheese[] getCheeses() {
      return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
    }
    ```

* 배열을 미리 할당 (성능이 나쁨)

    ```java
    return cheesesInStock.toArray(new Cheese[cheesesInStock.size()]);
    ```

## 핵심 정리

> null이 아닌, 빈 배열이나 컬렉션을 반환하라. null을 반환하는 API는 사용하기 어렵고
오류 처리 코드도 늘어난다. 그렇다고 성능이 좋은 것도 아니다.