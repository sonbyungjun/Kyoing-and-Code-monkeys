# 불필요한 객체 생성을 피하라

* 정규표현식 예제

    ```
    matches를 사용하면 내부에서 Pattern 인스턴스를 한 번 쓰고 바로 버려 진다고 한다. (아마도 null 처리를 하는 것이리라)
    Pattern이 유한상태머신(finite state machine)을 만들어서 인스턴스 생성 비용이 높다고 하는데 저걸 모르겠음
    ```

* 지연초기화에 대한

    ```java
    public class RomanNumerals {
        private static final Pattern ROMAN = Pattern.comile(...);

        static boolean isRomanNumeral(String s) {
            return ROMAN.matcher(s).matches();
        }
    }
    ```

    ```
    위의 코드에서 isROmanNumeral를 단 한번도 호출 하지 않는다면 ROMAN 필드의 초기화 즉 Pattern클래스의 인스턴스를 만들어놓은 것이 결국 낭비되는 셈이 될 수 있다는걸 말하는 거 같다 이를 해결할 수 있는 방법으로 lazy initialization을 이야기한다. 음... 이것은 생각보다 간단한 이야기인거같다.
    ```

    ```java
    public class RomanNumerals {
        private static Pattern ROMAN;

        static boolean isRomanNumeral(String s) {
            
            if(ROMAN == null) {
                ROMAN = Pattern.comile(...);
            }

            return ROMAN.matcher(s).matches();
        }
    }
    ```

    ```
    물론 변경한 소스는 참 좋아 보이진 않는다... 그래도 얼추 말하고 싶은본질은 표현 하는거같다.
    늘 객체를 만드는게 아니라 호출 하려는 시점에서 없을때 그때 그제서야 부랴부랴 객체를 만들어서 제공해주겠다 이런 의미라고 생각하면 될것같다... 그래서 lazy initialization이라고 하는거 같다.
    ```

* 아이템 50에 대한 언급

    ```
    아이템 50은 이번 아이템과는 대조적일거라고한다. 오히려 불필요한 객체 재사용을 피하고 객체를 만들어라 이런 내용이라고 하는데... 음... 추측해본다면 결합력에 대한 이야기를 하고 싶은거같다.
    SRP에 대한 이야기이지 않을까? 하는 생각을 한다.
    최근에 경험해본 예로들면 괴물처럼 증식해버리는 VO들..이런거 아닐까?
    ```