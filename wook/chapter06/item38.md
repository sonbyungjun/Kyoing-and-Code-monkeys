# 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

## 인터페이스를 이용한 확장 가능 열거 타입

```java
public interface Operation {
    double apply(double x, double y);
}

public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
```

## 예시 실행

```java
public static void main(String[] args) {
    double x = 2;
    double y = 4;
    test(BasicOperation.class, x, y);
  }

private static <T extends Enum<T> & Operation> void test(Class<T> opEnumType, double x, double y) {
    for (Operation operation: opEnumType.getEnumConstants()) {
        System.out.printf("%f %s %f = %f%n", x, operation, y, operation.apply(x, y));

    }
}
```

## 핵심 정리

```
열거타입 자체는 확장할 수 없지만 (상속하거나 구현 할 수 없지만), 인터페이스를 이용하면 같은효과를 낼 수 있다.

> 약간 커맨드패턴이랑 비슷한 인상을 받았다. 함수를 공통으로 뽑아내고 실행하는 예제의 모습이 사뭇 비슷했다.
```