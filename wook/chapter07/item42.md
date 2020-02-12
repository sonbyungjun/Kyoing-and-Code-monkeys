# 익명 클래스보다는 람다를 사용하라

## 핵심요약

### 과거 (JDK 1.7 이하의 버전)

* 함수 타입을 표현할 때 추상메서드를 하나만 담은 인터페이스를 사용했음.

* 함수 객체를 만드는 주요 수단은 익명 클래스를 이용 한 것이었고 과거 객체지향 디자인 패턴에는 익명 클래스면 충분 했음.

    ```java
    Collections.sort(words, new Comparator<String>() {
        public int compare(String s1, String s2) {
            return Integer.compare(s1.length(), s2.length());
        }
    });
    ```

* 하지만, 익명 클래스 방식은 코드가 너무 길기 때문에 함수형 프로그래밍에는 적합하지 않음.

### 최근 (JDK 1.8 이상의 버전)

* 파라미터의 타입에 대한 명시가 없더라도 컴파일러가 추론해서 결정해줌

* 타입 추론 규칙은 너무 복잡해서 이걸 다 이해하기란 쉬운일이 아니기 때문에 잘 알지 못한다 해도 괜찮음.

* __타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 파라미터 타입은 생략하자.__

    ```java
    Collections.sort(words,
        (s1, s2) -> Integer.compare(s1.length(), s2.length()));
    ```

> __Note:__ item26 (제네릭의 로 타입을 쓰지 마라), item29 (제네릭을 고려하라), item30 (제네릭 메서드를 고려하라)고 했는데, 람다를 사용할 때 두배로 중요해진다. 컴파일러가 타입을 추론 할 때 타입 정보 대부분을 제네릭에서 가져오기 때문이다.

<br>

* 비교자 생성 메서드를 사용하여 코드를 더 간결하게 만들기

    ```java
    Collections.sort(words, comparingInt(String::length));
    ```

* Java8 List 인터페이스에 추가된 sort 메서드를 이용하여 더 간결하게 만들기

    ```java
    words.sort(comparingInt(String::length));
    ```

* 람다를 지원하면서 기존에 적합하지 않았던 곳에서도 함수 객체를 실용적으로 사용할 수 있게 되었음.

    * 열거 타입에서 보았던 Operation 클래스

    ```java
    public enum Operation {
        PLUS("+") {
            public double apply(double x, double y) {return x + y;}
        },
        MINUS("-") {
            public double apply(double x, double y) {return x - y;}
        },
        TIMES("*") {
            public double apply(double x, double y) {return x * y;}
        },
        DIVIDE("/") {
            public double apply(double x, double y) {return x / y;}
        };

        private final String symbol;

        Operation(String symbol) {this.symbol = symbol;}

        public abstract double apply(double x, double y);
    }
    ```

    * Operation 클래스를 람다식으로 변경

    ```java
    public enum Operation {
        PLUS("+", (x, y) -> x + y),
        MINUS("-", (x, y) -> x - y),
        TIMES("*", (x, y) -> x * y),
        DIVIDE("/", (x, y) -> x / y);

        private final String symbol;

        private final DoubleBinaryOperator op;

        Operation(String symbol, DoubleBinaryOperator op) {
            this.symbol = symbol;
            this.op = op;
        }

        public double apply(double x, double y) {
            return op.applyAsDouble(x, y);
        }
    }
    ```

### 단점

* 이름이 없고 문서화도 못 한다. 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야한다. (세줄 안에 끝내야 한다.)

* 추상 클래스의 인스턴스를 만들 때 람다를 쓸수 없다.

* 자신을 참조할 수 없다. 람다에서 this 키워드는 바깥 인스턴스를 가리킨다. 반면 익명 클래스에서의 this는 자신의 인스턴스를 가리킨다.

* 직렬화 형태가 구현별로(가상머신별로) 다를 수 있기 때문에 __람다를 직렬화하는 일은 극히 삼가야 한다(익명 클래스도 마찬가지)__ 직렬화 해야하는 함수 객체가 있다면 private 정적 중첩 클래스의 인스턴스를 사용하자.

### 핵심 정리

```
익명 클래스는 타입의 인스턴스를 만들때만 사용하라. 그 외에는 함수형 인터페이스를 사용하자.
```