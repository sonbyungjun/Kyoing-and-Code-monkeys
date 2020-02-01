# 표준 함수형 인터페이스를 사용하라

## 핵심 정리

함수형 인터페이스를 직접 구현하여 사용 해도 되지만, java.util.function 패키지에 다양한 용도의 표준 함수형 인터페이스가 담겨져 있으니 __필요한 용도에 맞는게 있으면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하자.__ 

### 표준 함수형 인터페이스

java.util.function 패키지에는 총 43개의 인터페이스가 담겨 있다고 한다. 전부 기억하긴 어렵겠지만, 기본 인터페이스 6개만 기억하면 나머지는 유추해 낼 수 있다.

| 인터페이스 | 함수 시그니처 | 예 |
|---------|------------|---|
| UnaryOperator<T> | T apply(T t) | String::toLowerCase |
| BinaryOperator<T> | T apply(T t1, T t2) | BigInteger::add |
| Predicate<T> | boolean test(T t) | Collection::isEmpty |
| Function<T,R> | R apply(T t) | Arrays::asList |
| Supplier<T> | T get() | Instant::new |
| Consumer<T> | void accept(T t) | System.out::println |

### 전용 함수형 인터페이스 구현

* 자주 쓰이며, 이름 자체가 용도를 명확히 설명해준다.

* 반드시 따라야 하는 규약이 있다. (별도의 룰셋이 존재해야만 할 때?)

* 유용한 디폴트 메서드를 제공할 수 있다.

* @FunctionalInterface 애너테이션을 사용하는 것이 좋다. (마킹용도)

## 핵심

```
이제 자바도 람다를 제공하기 때문에 API를 설계할 때 람다도 염두해 두어야 한다. 보통은 java.util.function 패키지의 표준 함수형 인터페이스를 사용하는 것이 가장 좋은 선택이지만, 직접 새로운 함수형 인터페이스를 만들어 쓰는 편이 나을 수도 있다는 것을 잊지 말자.
```