# 인스턴스화를 막으려거든 private 생성자를 사용하라

* static class와 method를 만들고 싶을 때가 있다고 함. 예컨데, 유틸성 클래스를 이야기 하는 것 같음.

* 종종 인스턴스화 하는걸 막아놓지 않은 class들을 볼수가 있음. 근데 이거 막을 수 있음.

    * 어떻게? private 생성자를 만들면 됨.

```java
public Utils {
    private Utils() {}

    public static void doSomething() {...}
}
```