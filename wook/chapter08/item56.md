# 공개된 API 요소에는 항상 문서화 주석을 작성하라

## 핵심 요약

### 여러분의 API를 올바로 문서화하려면 공개된 모든 클래스, 인터페이스, 메서드, 필드 선언에 문서화 주석을 달아야 한다.

* 직렬화할 수 있는 클래스라면 직렬화 형태에 관해서도 적어야 한다.

### 메서드용 문서화 주석에는 해당 메서드와 클라이언트 사이의 규약을 명료하게 기술해야 한다.

* 상속용으로 설계된 클래스의 메서드가 아니라면 (그 메서드가 어떻게 동작하는지가 아니라) 무엇을 하는지를 기술해야 한다. 즉, how가 아닌 what을 기술해야 한다.

* 클라이언트가 해당 메서드를 호출하기 위한 전제조건(precondition)을 모두 나열해야 한다.

* 메서드가 성공적으로 수행된 후에 만족해야 하는 사후조건(postcondition)도 모두 나열해야 한다.

* 일반적으로 전제조건은 `@throws`태그로 비검사 예외를 선언하여 암시적으로 기술한다.

* 부작용도 문서화해야 한다.

    > 부작용이란? 사후조건으로 명확히 나타나지는 않지만 시스템의 상태에 어떠한 변화를 가져오는 것을 뜻함. 예컨대 백그라운드 스레드를 시작시키는 메서드라면 그 사실을 문서에 밝혀야 한다.

* 메서드의 계약(contract)을 완벽히 기술하려면 `@param` 태그를

* 반환 타입이 `void`가 아니라면 `@return` 태그를

* 발생할 가능성이 있는 (검사든 비검사든) 모든 예외에 `@throws` 태그를

* 각각의 코딩 표준에서 허락한다면 `@return` 태그의 설명이 메서드 설명과 같을 때 `@return` 태그를 생략해도 좋다

### 위 방법에 의거한 주석 사용 예시

```java
/**
* Returns the element at the specified position in this list.
*
* <p>This method is <i>not</i> guaranteed to run in constant
* time. In some implementations it may run in time proportional
* to the element position.
*
* @param  index index of element to return; must be
*         non-negative and less than the size of this list
* @return the element at the specified position in this list
* @throws IndexOutOfBoundsException if the index is out of range
* ({@code index < ø || index >= this.size()})
*/
E get(int index);
```

### 주석 사용 예시에서 주목 해야 할 점

* HTML 태그(`<p>`와 `<i>`)를 쓴 점

    1. 최종적으로 HTML 문서에 반영됨

    1. 드물긴 하지만 HTML table까지 넣는 프로그래머도 있음.

* `@throws`에 사용한 `{@code}` 태그의 효과

    1. 태그로 감싼 내용을 코드용 폰트로 렌더링함.

    1. 태그로 감싼 내용에 포함된 HTML요소나 다른 자바독 태그를 무시함.

* *"this list"*라는 단어에 주목하자

    1. 주석에서 쓰인 *"this"*는 호출된 메서드가 자리하는 객체를 가리킨다.

    1. item15에서 이야기했듯, 클래스를 상속용으로 설계할 때는 자기사용 패턴(self-use-pattern)에 대해서도 문서를 남겨서 올바로 재정의하는 방법을 알려줘야 함.

    1. @implSpec 태그로 문서화함.

    1. 일반적인 문서화 주석은 해당 메서드의 클라이언트 사이의 계약을 설명하지만, @implSpec 주석은 해당 메서드와 하위 클래스 사이의 계약을 설명함.

    1. 하위 클래스들이 그 메서드를 상속하거나 super 키워드를 이용해 호출할 때 그 메서드가 어떻게 동작하는지를 명확히 인지하고 사용하도록 해줘야 한다.

### `@implSpec` 태그 사용 예시

```java
/**
  * Returns true if this collection is empty.
  * 
  * @implSpec
  * This implementation returns {@code this.size() == 0}.
  * 
  * @return true if this collection is empty
  */
public boolean isEmpty() { ... }
```

### API 설명의 HTML 메타문자를 포함시키는 좋은 방법

* `{@literal}`
    
    1. `{@code}` 태그와 비슷하지만 코드포트로 렌더링하지는 않음.

### 각 문서화 주석의 첫 번째 문장은 해당 요소의 요약 설명으로 간주된다.

1. 요약 설명은 반드시 대상의 기능을 고유하게 기술해야 한다.

1. 마침표에 주의해야한다.

    > "머스터드 대령이나 Mrs. 피콕 같은 용의자." --> "머스터드 대령이나 Mrs." 까지만 요약 설명됨.

    * 좋은 해결 방법은 `{@literal}` 태그로 마침표를 감싸주는 것.

> (옮긴이) 자바 10부터는 `{@summary}`라는 요약 설명 전용 태그가 추가되어, 다음처럼 깔끔하게 처리 가능
>
> ```java
> /**
>  * {@summary A suspect, such as Colonel Mustard or Mrs. Peacock. }
>  */
> public enum Suspect { ... }
> ```
>

### API의 색인

* 자바9부터는 자바독이 생성한 HTML 문서에 검색 기능이 추가됨.

* `{@index}` 태그를 이용해서 API의 중요한 용어를 추가로 색인화할 수 있다.

```java
/**
 * This method complies with the {@index IEEE 754} standard.
 */
```

### 제네릭, 열거 타입, 애너테이션

* 제네릭 타입이나 제네릭 메서드를 문서화할 때는 모든 타입 매개변수에 주석을 달아야 함.

```java
/**
 * An object that maps keys to values . A map cannot contain
 * duplicate keys ; each key can map to at most one value.
 *
 * (Remainder omitted)
 *
 * @param <K> the type of keys maintained by this map
 * @param <V> the type of mapped values
 */
public interface Map<K, V> { ••• }
```

* 열거 타입을 문서화할 때는 상수들에도 주석을 달아야 한다.

```java
/**
 * An i nstrument section of a symphony orchestra.
 */
public enum OrchestraSection {
    /** Woodwinds, such as flute, clarinet, and oboe . */
    WOODWIND,
    /** Brass instruments, such as french horn and trumpet . */
    BRASS,
    /** Percussion instruments, such as timpani and cymbals. */
    PERCUSSION,
    /** Stringed instruments, such as violin and cello. */
    STRING;
}
```

* 애너테이션 타입을 문서화 할 때는 멤버들에도 모두 주석을 달아야 한다.

```java
/** * Indicates tilat the annotated method is a test method tilat
* must throw the designated exception to pass.
*/
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    /**
     * The exception that the annotated test method must throw
     * in order to pass. (The test is permitted to throw any
     * subtype of the type described by this class object.)
     */
    Class<? extends Throwable> value();
}
```

### 패키지를 설명하는 문서화 주석은 `package-info.java` 파일에 작성한다.

### 모듈 관련 설명은 `module-info.java` 파일에 작성한다.

### 클래스 혹은 정적 메서드가 스레드 안전하든 그렇지 않든, 스레드 안전 수준을 반드시 API 설명에 포함해야 한다.

## 핵심 정리

문서화 주석은 API를 문서화하는 가장 훌륭하고 효과적인 방법이다. 공개 API라면 빠짐없이 설명을 달아야 한다. 표준 규약을 일관되게 지키자. 문서화 주석에 임의의 HTML태그를 사용할 수 있음을 기억하라. 단, HTML 메타문자는 특별하게 취급해야 한다.