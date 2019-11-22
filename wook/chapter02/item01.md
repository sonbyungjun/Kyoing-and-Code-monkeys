# 생성자 대신 정적 팩터리 메서드를 고려하라
## [Advantage](#Advantage)
* [이름을 가질 수 있다.](#이름을-가질-수-있다.)

* [호출 될 때마다 인스턴스를 새로 생성하지 않아도 된다.](#호출-될-때마다-인스턴스를-새로-생성하지-않아도-된다.)

* [리턴 타입의 하위 타입 객체를 리턴 할 수 있는 능력이 있다.](#리턴-타입의-하위-타입-객체를-리턴-할-수-있는-능력이-있다.)

* [파라미터에 따라서 매번 다른 클래스의 객체를 리턴 할 수도 있다.](#파라미터에-따라서-매번-다른-클래스의-객체를-리턴-할-수도-있다.)

* [Static factory method를 작성하는 시점에는 반활할 객체의 클래스가 존재 하지 않아도 된다.](#Static-factory-method를-작성하는-시점에는-반활할-객체의-클래스가-존재-하지-않아도-된다.)

## [Disadvantage](#Disadvantage)
* [Static factory method만 제공하면 Sub class를 만들 수 없다.](#Static-factory-method만-제공하면-Sub-class를-만들-수-없다.)

* [개발자가 객체생성 방법을 찾기 어렵다.](#개발자가-객체생성-방법을-찾기-어렵다.)

<br>

# Advantage
## 이름을 가질 수 있다.
* 핵심요약
    * 생성자 만으로는 리턴 할 객체의 특성을 설명하기 어렵다.
    * 잘 활용하면, API 문서를 보지않더라도 객체의 특성에 대해서 파악할 수 있다.

* 예시
```java
BigInteger.probablePrime // 소수값을 리턴한다는 의미
```

## 호출 될 때마다 인스턴스를 새로 생성하지 않아도 된다.
* 핵심요약
    * 인스턴스를 미리 만들어놓거나 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요하게 객체를 생성하는 것을 피할 수 있다.
    * 이것을 활용하면 singleton이나 noninstaniable로 만들 수도 있다.

## 리턴 타입의 하위 타입 객체를 리턴 할 수 있는 능력이 있다.
* 핵심요약
    * 리턴할 객체의 클래스를 자유롭게 선택할 수 있게 한다.
    ```mermaid
    graph LR;
        s(start) --> Object
            Object --> r{Select}
            r --> |A|a[A class] --> return
            r --> |B|b[B class] --> return
            return[Return Object] --> st
        st(stop)
    ```

## 파라미터에 따라서 매번 다른 클래스의 객체를 리턴 할 수도 있다.
* 예시
    ```mermaid
    graph LR;
        s(start) --> EnumSet
            EnumSet --> 65{65}
            65 --> |greater|JumboEnumSet --> return
            65 --> |less|RegularEnumSet --> return
            return[Return Object] --> st
        st(stop)
    ```

## Static factory method를 작성하는 시점에는 반활할 객체의 클래스가 존재 하지 않아도 된다.
* 핵심요약
    * Service provider framework를 만드는 근간이 됨.
    ```
    어떤 Datasource가 온다 하더라도 우리는 DriverManager.getConnection만 하면 된다 이런거 아닌가 싶음.
    ```

<br>

# DisAdvantage
## Static factory method만 제공하면 Sub class를 만들 수 없다.
* 핵심요약
    * 상속하려면 public 혹은 protected 생성자가 필요하기 때문.

    * 하지만 어찌보면 이거는 상속보다 컴포지션을 사용 하도록 유도하고 불변 타입으로 만들려고 하면 이 제약을 지켜줘야 하니까 상황에 따라서 장점이 될 수도 있다.

## 개발자가 객체생성 방법을 찾기 어렵다.
* 핵심요약
    * API설명이 명확하지는 않으니 인스턴스화 하는 방법에 대해서 알아내야 한다.
    ```
    이름을 명확하게 짓는 방법 정도 혹은 doc주석에 따로 해당 내용 명시하는 것 그 정도로 완화 할 수 있지 않을까? 생각함.
    ```