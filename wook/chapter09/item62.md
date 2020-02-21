# 다른 타입이 적절하다면 무자열 사용을 피하라

## 핵심 요약

`String`은 텍스트를 표현하도록 설계되었고, 그일을 잘 해냄. 그런데 문자열은 워낙 흔하고 자바가 또 잘지원해주어 원래 의도하지 않은 용도로도 쓰이는 경향이 있음.

## 의도하지 않은 용도로 쓰이는 사례

* 문자열은 다른 값 타입을 대신하기에 적합하지 않다.

  > 많은 사람이 파일, 네트워크, 키보드 입력으로부터 데이터를 받을때 주로 문자열을 사용함. 자연스러워 보이지만, 입력받을 데이터가 진짜 문자열일때만 그렇게 하는게 좋음.

* 문자열은 열거 타입을 대신하기에 적합하지 않다.

  > 상수를 열거할 때는 문자열보다는 열거 타입이 월등히 낫다.

* 문자열은 혼합 타입을 대신하기에 적합하지 않다.

  > 여러 요소가 혼합된 데이터를 하나의 문자열로 표현하는 것은 대체로 좋지 않은 생각이다.

  * 혼합 타입을 문자열로 처리한 부적절한 예

    ```java
    String compoundKey = className + "#" + i.next();
    ``` 

    > 단점이 많은 방식임 혹여라도 두요소를 구분해주는 문자 **#**이 두 요소중 하나에서 쓰였다면 혼란스러운 결과를 초래한다. 각 요소를 개별로 접근하려면 문자열을 파싱해야 해서 느리고, 귀찮고, 오류 가능성도 커진다. 적절한 euquals, toString, compareTo 메서드를 제공할수 없으며, String이 제공하는 기능에만 의존해야 한다. 그래서 차라리 전용 클래스를 만드는 편이 낫다.

* 문자열은 권한을 표현하기에 적합하지 않다.

  > 권한(capacity)을 문자열로 표현하는 경우가 종종 있다.

  * 잘못된 예 - 문자열을 사용해 권한을 구분

    ```java
    public class ThreadLocal {
      private ThreadLocal() {} // 객체 생성 불가

      // 현 스레드의 값을 키로 구분해 저장한다.
      public static void set(String key, Object value);

      // (키가 가리키는) 현 스레드의 값을 반환한다.
      public static Object get(String key);
    }
    ```

    > 스레드 구분용 문자열 키가 전역 이름공간에서 공유된다는 점이 문제점  
    > 이 방식이 의도대로 동작하려면 각 클라이언트가 고유한 키를 제공해야 함. 그런데 만약 두 클라이언트가 서로 소통하지 못해 같은 키를 쓰기로 결정한다면, 의도치 않게 같은 변수를 공유하게 됨.

  * Key 클래스로 권한을 구분

    ```java
    public class ThreadLocal {
      private ThreadLocal() {} // 객체 생성 불가

      public static class Key { // (권한)
        Key() {}
      }

      // 위조 불가능한 고유 키를 생성한다.
      public static Key getKey() {
        return new Key();
      }

      public static void set(Key key, Object value);
      public static Objet get(Key key);
    }
    ```

    > 문자열 기반의 API의 문제 두가지를 모두 해결해주지만, 개선할 여지가 있음.  
    > * set과 get은 이제 정적 메서드일 이유가 없으니 Key 클래스의 인스턴스 메서드로 변경.  
    >
    >   이렇게 하면 Key는 더이상 스레드 지역변수를 구분하기 위한 키가 아니라, 그 자체가 스레드 지역변수가 된다. 결과적으로 톱레벨 클래스인 `ThreadLocal`은 별달리 하는 일이 없어지므로 치워버리고, 중첩클래스 Key의 이름을 `ThreadLocal`로 바꿔버리자.

  * 리팩토링하여 Key를 ThreadLocal로 변경

    ```java
    public final class ThreadLocal {
      public ThreadLocal();
      public void set(Object value);
      public Object get();
    }
    ```

    > get으로 얻은 Object를 실제 타입으로 형변환해 써야 해서 타입 안전하지 않음.

  * 매개변수화하여 타입안전성 확보

    ```java
    public final class ThreadLocal<T> {
      public ThreadLocal();
      public void set(T value);
      public T get();
    }
    ```

## 핵심 정리

더 적합한 데이터 타입이 있거나 새로 작성할 수 있다면, 문자열을 쓰고 싶은 유혹을 뿌리치자.  
문자열은 잘못 사용하면 번거롭고, 덜 유연하고, 느리고, 오류 가능성도 크다.  
문자열을 잘못 사용하는 흔한 예로는 기본타입, 열거타입, 혼합타입이 있다.