# 람다보다는 메서드 참조를 사용하라

## 핵심 정리

### 예시

* 람다 사용

```java
map.merge(key, 1, (count, incr) -> count + incr);
```

* 메서드 참조 사용

```java
map.merge(key, 1, Integer::sum);
```

### 장점

* 간결함

* 메서드 이름으로 표현되기 떄문에 더 명시적으로 표현할 수 있다.

### 예외

메서드 참조가 람다보다 간결하지 않을 때도 존재한다.

* 람다 사용

```java
service.execute(GoshThisClassNameIsHumongous::action);
```

* 메서드 참조 사용 

```java
service.execute(() -> action());
```

> 메서드 참조 사용을 하는 편이 훨씬 간결함.

### 메서드 참조 사용 사례들

| 메서드참조유형 | 예 | 같은 기능을 하는 람다 |
|------------|----|------------------|
| 정적 | Integer::parseInt | str -> Integer.parseInt(str) |
| 한정적 (인스턴스) | Instant.now()::isAfter | Instant then = Instant.now(); <br> t -> then.isAfter(t) |
| 비한정적 (인스턴스) | String::toLowerCase | str -> str.toLower() |
| 클래스 생성자 | TreeMap<K,V>::new | () -> new TreeMap<K,V>() |
| 배열 생성자 | int[]::new | len -> new int[len] |

## 핵심

```
메서드 참조는 람다를 간결하게 만드는 대안이 될 수 있음. 메서드 참조 쪽이 짧고 명확하다면 메서드 참조를 쓰고, 그렇지 않을 때만 람다를 사용하자.
```

## 메서드 참조로 가능한 유일한 예

제네릭 함수 타입 구현

```java
interface G1 {
  <E extends Exception> Object m() throws E;
}

interface G2 {
  <F extends Exception> String m() throws Exception;
}

interface G extends G1, G2 {}

이떄 함수형 인터페이스 G를 함수 타입으로 표현하면 다음과 같다.

<F extends Exception> () -> String throws F

메서드 참조 표현식으로는 해당 제네릭 함수 타입 구현이 가능하지만, 람다식으로는 불가능함.
제네릭 람다식 문법이 없음.
```