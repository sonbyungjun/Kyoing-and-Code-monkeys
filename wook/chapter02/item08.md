# finalizer와 cleaner사용을 피하라

```
보통 자원 해제할때 우리가 쓰는 코드는 이런식일 것이다
```

```java
try {
    // this is closeable class
    Something something = new Something();
    ...
} catch(Exception e) {
    e.printStackTrace();
} finally {
    if (something != null) {
        something.close();
    }
}
```

```
finalizer사용을 피하라는 것은 이것을 이야기 하는 것 같지는 않다.
아마도 이하의 내용을 이야기 하는거 아닐까 싶다
```

```java
class Test {    
    void doSomething(Something something) {
        ...
    }

    @Override
	public void finalize()
        try {
            something.close();
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

```
이렇게 해버리면 Test가 null이 되는 시점 즉, GC가 Test를 회수하지 않는 이상은  finalize의 실행을 기약할 수 없게 된다. 이걸 말하는거 같다.
```