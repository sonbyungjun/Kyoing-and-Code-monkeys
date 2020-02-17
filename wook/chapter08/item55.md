# 옵셔널 반환은 신중히 하라

## 핵심 요약

### 자바 8 이전 `null`처리 방법

1. 특정조건에서 값을 반환할 수 없을 때 예외를 던짐

1. (반환 타입이 레퍼런스 참조라면) `null`을 반환하는 것이 있었음. 두 방법 모두 허점이 있음.

### 허점

#### 예외처리

1. 예외는 진짜 예외적인 상황에서만 사용해야 함.

1. 예외를 생성할 때 스택 추적 전체를 캡처하니까 비용도 만만치 않음.

#### `null` 반환

1. (`null`이 반환될 일이 절대 없다고 확신하지 않는 한) 별도의 `null` 처리 코드를 추가해야 함.

1. `null`처리를 무시하고 반환된 `null`값을 저장해두면 언젠가 `NullPointerException`이 발생할 수 있음.

### 옵션널 이용

옵셔널은 원소를 최대 1개 가질 수 있는 **'불변'** 컬렉션이다

> **Note** `Optional<T>`가 `Collection<T>`를 구현하지는 않았지만, 원칙적으로 그렇다는 말

보통은 `T`를 반환해야 하지만 특정조건에서 아무것도 반환하지 않아야 할 때 `T` 대신 `Optional<T>`를 반환하도록 선언하면 됨.

#### 옵셔널 이용예시

* 최대값을 구하는 메서드 (예외처리)

```java
public static <E extends Comparable<E>> E max(Collection<E> c) {
  if (c.isEmpty()) {
    throw new IllegalArgumentException("빈 컬렉션");
  }

  E result = null;
  for (E e : c) {
    if (result == null || e.compareTo(result) > 0) {
      result = Objects.requireNonNull(e);
    }
  }
  return result;
}
```

* 최대값을 구하는 메서드 (옵셔널)

```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
  if (c.isEmpty()) {
    return Optional.empty();
  }

  E result = null;
  for (E e : c) {
    if (result == null || e.compareTo(result) > 0) {
      result = Objects.requireNonNull(e);
    }
  }
  return Optional.of(result);
}
```

* 최대값을 구하는 메서드 (옵셔널 && 스트림)

```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
  return c.stream().max(Comparator.nauralOrder());
}
```

#### 옵셔널 활용

* 기본값을 정해둘 수 있다

```java
String lastWordInLexicon = max(words).orElse("단어 없음...");
```

* 원하는 예외를 던질 수 있다

```java
Toy myToy = max(toys).orElseThrow(TemperTantrumException::new);
```

* 항상 값이 있다고 가정 함.

```java
Element lastNobleGas = max(Elements.NOBLE_GASES).get();
```

#### 기본값 설정 비용의 이슈

기본값을 설정하는 비용이 아주 커서 부담이 될 때는 `Supplier<T>`를 인수로 받는 `orElseGet`을 사용하면, 값이 처음 필요할 때 `Supplier<T>`를 사용해 생성하므로 초기 설정 비용을 낮출 수 있음.

더 특별한 쓰임에 대비한 메서드도 있음. (`filter`, `map`, `flatMap`, `ifPresent`)

* map을 이용한 다듬기

```java
/** before **/
Optional<ProcessHandle> parentProcess = ph.parent();
System.out.println("부모 PID: " + (parentProcess.isPresent() ?
    String.valueOf(parentProcess.get().pid()) : "N/A"));
```

```java
/** after **/
System.out.println("부모 PID: " +
    ph.parent().map(h -> String.valueOf(h.pid())).orElse("N/A"));
```

* 스트림을 사용하여 처리하는 경우

```java
/** java 8 **/
streamOfOptionals
    .filter(Optional::isPresent)
    .map(Optional::get)
```

> 옵셔널에 값이 있으면 `Optional::isPresent` 그 값을 꺼내 `Optional::get` 스트림에 매핑한다.

```java
/** java 9 **/
streamOfOptionals
    .flatMap(Optional::stream)
```

* 주의

    * 컬렉션, 스트림, 배열, 옵셔널 같은 컨테이너 타입은 옵셔널로 감싸면 안 된다.

        >  `Optional<List<T>>` 보다 빈 'List<T>'를 반환하는 게 좋다
        >
        > 참고로 `ProcessHandle.Info` 인터페이스의 arguments 메서드는 `Optional<String[]>`을 반환하는데, 이는 예외적인 경우이니 따라하지 말자.

#### 옵셔널 활용 규칙

* 결과가 없을 수 있으며, 클라이언트가 이 상황을 특별하게 처리해야 할때 `Optional<T>`를 반환한다.

* 기본타입에 대해서는` OptionalInt`, `OptioanlLong`, `OptionalDouble`과 같은 클래스를 이용하자.

    > 단, **'덜 중요한 기본 타입'**용인 `Boolean`, `Byte`, `Char`, `Short`, `Float`은 예외 일 수 있다.

* 옵셔널을 맵의 값으로 사용해서는 절대 안된다.

* 옵셔널을 인스턴트 필드에 저장해두고 사용하는 것이 보통은 나쁘지만, 가끔은 적절한 상황도 있음.

    > item02의 `NutritionFacts` 클래스

## 핵심 정리

값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야하는 메서드라면 옵셔널을 반환해야 할 상황일 수 있다. 하지만 옵셔널 반환에는 성능 저하가 뒤따르니, 성능에 민간함 메서드라면 `null`을 반환하거나 예외를 던지는 편이 나을 수 있다. 그리고 옵셔널을 반환값 이외의 용도로 쓰는 경우는 매우 드물다.