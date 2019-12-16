# 1.equals는 일반 규약을 지켜 재정의하라.
equals는 재정의하기 쉬워 보이지만 곳곳에 함정이 있다.  
이 함정을 피하는 방법은 재정의 하지 않는것이다.
아래중 한개라도 해당이되면 하지 않는것이 좋다.
1. 각인스턴스가 본질적으로 고유하다.
    - 값을 표현하는게 아닌 동작하는 개체를 표현하는 클래스가 여기에 해당한다.
    - Thread가 제일 좋은예이다.
2. 논리적 동치성을 검사할 일이 없다.
    - Pattern을 정의해서 논리적(logical equailty)을 검사가 가능하다.
    - 설계자는 클라이언트가 이 방식을 원하지 않거나 애초에 필요하지 않다고 판단할 경우 기본 Object equals로 가능하다.
3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
    - Set이나 List나 Map은 AbstractMap을 상속받아 구현한다.
4. 클래스가 private이거나 private-package일 경우 equals 메서드를 호출할 일이 없다.
    - equals안에 Exception을 발생시켜라

## 그렇다면 언제 재정의 하는가?
객체 식별성( 두 객체가 물리적으로 같은가 )이 아니라 논리적 동치성을 확인해야 하는데.  
상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의가 안되어있을때다.
주로 값 클래스가 여기에 해당된다.
## 값 클래스란 ?
Integer,String 처럼 값을 표현하는 클래스이다.
두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶은 것이다.

Equals를 논리적 동치성을 확인하도록 재정의 할경우 그인스턴스는 값을 비교하길 원하는 프로그래머의 기대의 부흥을 넘어서
Map의 키와 Set의 원소로 사용이 가능하다.

값 클래스라 해도 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의 하지 않아도 된다. (Enum)  
이러한 경우는 어차피 논리적으로 같은 인스턴스가 2개가 만들어지지 않으니 문제가없다.
## euquals 메서드를 재정의시 반드시 지켜야 할 규약
`
equals 메서드는 동치관계를 구현하며, 다음을 만족한다.
1. 반사성 : null 이 아닌 모든 참조 값 x에 대해 x.equals(x)는 true이다.
    - 객체는 자기 자신과 같아야 한다.
2. 대칭성 : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)가 true면 y.equals(x)는 true이다.
    - 두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다는 조건이다.
3. 추이성 : null이 아닌 모든 참조 값 x,y,z,에 대해, x.equals(y)가 true이면 x.equals(z)도 true이면 x.equals(z)도 true이다.
    - 첫 객체와 두 객체가 같고 두 객체가 세번째 객체와 같으면 첫 객체는 세번째 객체와 같아야 한다.
4. 일관성 : null이 아닌 모든 참조 값 x,y에 대해 x.equakls(y)를 반복해서 호출하면 항상 true또는 false를 반환한다.
    - 두 객체가 다르게 수정되지 않는 한 앞으로도 영원히 같아야 한다.
5. null-아님 : null이 아닌 모든 참조 값 x에 대해 x.equals(null)은 false이다.

사실 위에 내용은 같은 클래스가 아닌 다른 클래스였을때도 동작했으면 한다.  
리스코프 치환원칙을 지키면서 구현해야한다.
`
## 동치관계란?
집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산.
equals 메소드가 쓸모 있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 한다.


# 2. equals를 재정의 하려거든 hashcode도 재정의 하라
** equals를 재정의한 클래스 모두에서 hashcode도 재정의 해야 한다. **
만약 재정의 하지 않을경우 hashset이나 hashmap에서 같은 컬렉션의 원소로 사용할 때 문제를 일으킬 것이다.
## Object 명세
1. equals 비교에 사용되는 정보가 변경하지 않았다면은 어플리케이션이 재실행 까지 hashcode는 변하지 않아야 한다.
2. equals가 두 객체를 같다고 판단했을경우 hashCode도 같아야한다.
3. equals가 두 객체를 다르다고 판단했더라고 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

**논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.**  
`
Map User = new HashMap();  
User.put(new TelNo(123,456,7890), "제니");
User.get(new TelNo(123,456,7890)) -> null return
`
위에 예제는 다른 hashcode를 갖고있기 때문에 return null이 떨어진다.
두번째에서 논리적 동치인 객체를 사용하여 꺼내려고 했으니 HashCode가 같지 않기에 null이 나온것이다.
## 최악의 ( 하지만 적법한 ) hashCode 구현 - 절대 사용 X
`
public int hashCode() { return 42; }
`  
위에 코드로 사용할 경우 해쉬맵의 O(1)은 O(N)으로 변하게 된다.  
인스턴스는 32비트로 적당히 분배가 잘 되어야한다.
## hashcode 만들시 참고할점
1. 만들고 나서 Test코드로 equals와 hashcode가 같은지 테스트 해야한다. (물론 자동으로 만들어진것이면 뛰어넘어도 된다)
2. 파생 필드는 해시코드 계산에서 제외해도 된다.  
3. equals에 사용하지 않은 객체는 무조건 제외해야한다.  
4. Object.hash나 Guava의 Hash를 사용해도된다. ( 하지만 Object.Hash는 느리다 )  
5. 객체가 불변이며 해시코드를 만드는데 시간이 많이 걸린다면은 캐싱하여 사용하자.  
6. 성능을 높인다고 중요한 필드를 빼먹거나 짤라서 사용하지 말아야 한다 ( 자바2의 String이 그러했음. )
# 3. toString을 항상 재정의하라
- Object의 기본 toString 메서드가 우리가 작성한 클래스에 적합한 문자열을 반환하느 경우는 거의 없다.
- 기본적으로 className@HashCode를 반환은 한다.
- toString 일반 규약에 따르면 ' 간결하고 사람이 읽기 쉬운 형태의 유익한 정보 '를 반환해야 한다.
- toString을 잘 구현한 클래스는 사용하기에도 즐겁고 디버깅도 쉽다.
- toString은 println,printf, 문자열 +, assert구문에서 사용한다. 결국 호출을 안해도 무의식적으로 사용이 된다.
- 컬렉션등의 객체에서 출력할 때에도 유용히 쓰여진다.
- 실전에서 toString은 그 객체가 갖고 있는 주요 정보 모두를 반환하는게 좋다.
- toString을 반환할떄 문서화를 할지 고민해봐야한다 ( Json,CSV등 )
    - 사람이 읽기 편하게된다.
        - 만약에 문서로 제공하게 된다면은 문서롤 객체로 변경할 수 있는 정적팩터리 메서드를 제공해도 좋을것이다.
    - 포맷을 명시하면은 평생 그 포멧에 얽매이게 된다.
- 포멧 명시 여부와 상관없이 toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API는 제공해야한다.
- 정적 유틸클래스나 Enum은 재정의 해줄 필요가 없다.
- 하위클래스들이 공유해야 할 문자열 푷녀이 있는 추상클래스라면 toString을 재정의 해야 한다.

# 4. clone 재정의는 주의해서 진행하라
- Cloneable은 복제해도 되는 클래스임을 명시하는 인터페이스지만 의도한 목적을 이루지 못했다.
    - 가장 큰 이유는 clone의 함수를 Cloneable에 있는것이 아니라 Object에 있는것이다 
        - [Cloneable](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/lang/Cloneable.java)
        - [Object](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/lang/Object.java)
    - Object의 Clone메소드는 protected로 외부에서 호출이 불가능하다.
        - 호출하려면 Reflection으로 해야하며 100%의 성공률을 보이지 못한다.
- Cloneable 인터페이스는 Object의 protected된 clone함수의 동작을 결정한다.
    - Cloneable이 implements가 안되어있으면은 clone호출시 에러가 발생된다. (java.lang.CloneNotSupportedException)
- 실무에서 Cloneable을 구현한 클래스의 clone 메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지리라 기대한다.  
 이기대를 만족시키려면은 그 클래스와 모든 상위 클래스는 복잡학고, 강제할 수 없고, 허술하게 기술된 프로토콜을 지켜야만 하는데, 그 결과로 깨지기 쉽고  
 모순적인 매커니즘이 탄생이 되며, 생성자를 호출하지 않아도 객체를 생성할 수 있게 된다.
    `
        public class MyNumbers implements Cloneable {
           private int[] numbers = null;
        
           public MyNumbers() {
              numbers = new int[10];
           }
            
           public Object clone() throws CloneNotSupportedException {
              return super.clone();
           }
        }
- super.clone을 사용하지 않고 new MyNumbers()를 호출해도 되지만 하위클래스에서 super.clone() 호출시 잘못된 클래스가 만들어지게 된다.
    - 만약 final로 클래스를 선언하면 문제가 되지 않는다.
- 가변 객체를 참조하는 순간 생각할 범위는 넓어진다.
    - 아래 코드를 보면은 size는 정상적으로 Copy가 되지만 elements는 같은 인스턴스를 바라보게 될것이다.
    - 가장 간단한것은 clone을 구현할때 elements.clone()으로 카피하며 된다.
    `
     public class Stack {
        private Object[] elements;
        private int size = 0;
        private static final int DEFAULT_INITIAL_CAPACITY = 16;
        public Stack() {
            this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
        }
        public void push(Object e) {
            ensureCapacity(); elements[size++] =e;
        }
        public Object pop() {
            if (size == 0)
                throw new EmptyStackException(); 
            Object result = elements[--size]; 
            elements[size] =null; // 다 쓴 참조 해제
            return result;
        }
         // 원소를 위한 공간을 적어도 하나 이상 확보한다.
         private void ensureCapacity() {
            if (elements. length == size)
                elements = Arrays.copyOf(elements , 2 * size + 1);
         }
     }
     `
- 많은 위험성이 있으므로 복사 생성자와 복사 팩터리라는 더 나은 객체 복사 방식을 제공하는것이 낫다.
# 5. Comparable을 구현할지 고려하라
- Comparable 인터페이스의 CompareTo는 성격이 Object의 equals와 비슷하다
- CompareTo는 단순 동치성 비교에 더해 순서까지 비교가 가능하며 제너릭 하다
- Comparable을 구현한다는 것은 그 클래스의 인스터들 사이에 자연적인 순서가 있다는 것이다.
- 검색, 극단값 계산, 자동 정렬되는 컬렉션 관리도 쉽게 가능하다.
- Comparable을 사용하면은 제네릭 알고리즘과 컬렉션의 힘을 얻을 수 있다.
- compareTo 메서드의 일반 규칙은 equals의 규약과 비슷하다
    - 이 객체가 주어직 객체보다 적으면 음의정수
    - 이 객체가 주어진 객체와 같은면 0
    - 이 객체가 주어진 객체보다 크면은 양의정수
    - 비교할 수 없으면 ClassCastException을 발생
- CompareTo를 구현하면 TreeSet, TreeMap, Collections, Arrays가 사용이 가능하다
- equals와 같이 반사성, 대칭성, 추이성을 충족해야 한다.
    - A == B && B == A
    - A > B && B < A
    - A < B && B < C && A < C  
- HashSet과 TreeSet에서의 차이
    - new BigDecimal("1.00") new BeigDecimal("1.0") 을선언
    - HashSet에다가 2개를 넣으면은 equals로 되어서 1개만 들어간다
    - TreeSet에다가 2개를 넣으면은 compareTo로 되어서 1개만 들어간
- compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같아야 한다.
- 정적메서드인 Dobule.compare Float.compare를 피해라
    - 자바 7부터는 Comparator를 사용하라
- hashcode의 값 차이로 비교하면 안된다.
    - 할것이면 Integer.compare(o1.hashCode(), o2.hashCode()) 를 사용하라
    - 할것이면 Comparator.comparingInt(o -> o.hashCode()) 를 사용하라