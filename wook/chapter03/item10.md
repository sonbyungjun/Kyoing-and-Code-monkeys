## 재정의 하면 안되는 이유

* 각 인스턴스가 본질적으로 고유하다.

* 인스턴스의  `logical equality`를 검사할 일이 없다.

*  상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.

    ```
    여담이지만, 부모클래스-자식클래스 관계로 이야기 하지 않고 상위클래스-하위클래스 
    라고 이야기한 부분이 상당히 마음에 든다.
    ```

* 클래스가  private이거나 package-private이고 equals 메서드를 호출할 일이 없다.

    ```java
    @Override public boolean equals(Object o) {
        throw new AssertionError(); // 호출 금지
    }
    ```

    > 호출할 일이 없다. 라는 것은 글쎄 잘 모르겠다.. 호출할 일이 없다고 단정짓는 것은 좀 섣부르지않나?
    > 라는 생각이든다. 그래서 AssertionError throw하는 코드를 넣은 것이 아 
    > 닌가 싶다

-

## 재정의 해야 할 때

* 값에 대한 비교를 해야 할 때

    * String

    * Integer

    > 값에 의한 비교를 해야 할 때가 있을 수도 있다 그런 상황을 이야기 하는 것 같다.  
    > 아래 상황을 기대하는 것 아닌가? 하는 생각을 한다.
    > 
    > ```java
    > public class User {
    >     private String name; 
    >     private int age;
    >
    >     public User(final String name, final int age) {
    > 		    this.name = name;
    > 		    this.age = age;
    >     }
    >     
    >     Getters and Setters
    >
    >     @Override
    >     public boolean equals(final Object obj) {
    > 	    if (obj instanceof User == false) {
    > 		    return false;
    > 	    }
    > 	    User user = (User) obj;
    > 	    return user.age == this.age && user.name == this.name;
    >     }
    > }
    > 
    > public static void main(String[] args) {
    >     User wook = new User("wook", 2);  
    >     User wookClone = new User("wook", 2);
    >     
    >     wook.equals(wookClone); // true를 기대함.
    > }
    > 
    > 
    > ```

* equals는 `equivalence relation`를 구현하며, 다음을 만족함.

    * 대전제: null이 아닌 모든 참조 값 x, y, z

    * reflexivity: x.equals(x) == true

    * symmetry: x.equals(y) == y.equals(x)

    * transitivity: (x.equals(y) && y.equals(z)) == x.equals(z)

    * consistency:

    ```java
    while(true) {
        System.out.println(x.equals(y)); // true
    }
    ```

    * not null: x.equals(null) == false

-

## 올바른 equals 사용 방법

* == 연산자를 사용해서 자기 자신의 참조인지 확인한다.

    ```
     reflexivity 해결
    ```

* instanceof 연산자로 입력이 올바른 타입인지 확인한다.

    ```
    symmetry 해결
    ```
    
    > 앞에 이야기하기를 상속이나 구현에 의해서 symmetry가 무너 질 수 있다고 하는데 그 해결법으로 생각한다.

* 올바른 타입으로 형변환한다.

    ```
    instanceof 검사를 했으면 100% 성공이다
    ```

* 입력객체와 자기 자신의 대응 되는 핵심 필드들이 대응 되는지 하나씩 확인한다.

    ```java
    public class Point {
        private final int x;
        private final int y;
    
        @Override public boolean equals(Object o) {
            if(!(instaceof Point)) {
                return false;
            }
            Point p = (Point) o;
            return p.x == x && p.y == y;
        }
    }
    ```

    > 멤버필드에 있는 `int x`와 `int y` 둘다 비교 하는 것에 대한 이야기라고 생각함

* equals를 다 구현했으면, 자문해보자 `symmetry`, `transitivity`, `consistency` 다 만족하느냐? 자문만하지말고 테스트까지 해보자~

* hashCode도 반드시 재정의하자 [아이템11] // link걸어야함

    > 이거는 아이템11에서 더 자세히 다룰거같음

* AutoValue를 이용해서 작성하면 테스트 할 필요도 없다.

    > AutoValue를 쓰라는거 아닌가? 싶다

* AutoValue 사용방법

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.google.auto.value</groupId>
        <artifactId>auto-value</artifactId>
        <version>${auto-value.version}</version>
        <optional>true</optional>
      </dependency>
    </dependencies>
    ```

    ```java
    import com.google.auto.value.AutoValue;
    
    @AutoValue
    abstract class Animal {
      static Animal create(String name, int numberOfLegs) {
        return new AutoValue_Animal(name, numberOfLegs);
      }
    
      abstract String name();
      abstract int numberOfLegs();
    }
    ```
    
    ```java
    public void testAnimal() {
      Animal dog = Animal.create("dog", 4);
      assertEquals("dog", dog.name());
      assertEquals(4, dog.numberOfLegs());
    
      // You probably don't need to write assertions like these; just illustrating.
      assertTrue(Animal.create("dog", 4).equals(dog));
      assertFalse(Animal.create("cat", 4).equals(dog));
      assertFalse(Animal.create("dog", 2).equals(dog));
    
      assertEquals("Animal{name=dog, numberOfLegs=4}", dog.toString());
    }
    ```