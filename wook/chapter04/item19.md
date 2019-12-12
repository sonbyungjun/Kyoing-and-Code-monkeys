# 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

## 핵심정리

```
상속용 클래스를 설계하는 것은 만만치 않음.
클래스내부에서 무엇을 하는지 문서로 남겨야하며, 문서화한 내용을 반드시 지켜야한다.
안그러면 오작동을 유발할 수 있다. 클래스를 확장해야 할 이유가 명확하게 떠오르지 않는다?
그러면 상속을 금지하자.
```

<br>

## 생각정리

```
이 부분 또한 LSP의 연장선이지 않나 싶다. final이나 생성자 private으로 상속을 금지하는 그런것이 좋을것같다. 확장해야할 클래스를 만드는 일은 일단, 웹개발을 하면서는 별로 없었던거 같다는게 나의 생각이다. 대규모서비스를 해야하는 그런 환경이라면....음.. 글쎄 그런 라이브러리를 제공해줘야한다라면 쉽지 않은 일임이에는 분명하다.

마구자비로 상속하는 그런 실무코드 뭐가 있을까 생각을 해봤는데 이런거 아닐까? 라는 생각이 든다.
```

```java
public class CommonVO {
    private String query;
    private String option;
    private String startDt;
    private String endDt;

    ...

    Getters And Setters
}

public class MenuVO extends CommonVO {
    private String menuId;
    private String url;

    ...

    Getters And Setters
}

public class BoardVO extends MenuVO {
    private String title;
    private String content;
    private String author;

    ...

    Getters And Setters
}
```