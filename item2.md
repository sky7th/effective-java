## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라

### 생성자:

-   java같은 언어는 메서드 정의 시 파라미터 default값을 정의할 수 없어서, 이를 흉내내려면 생성자를 굉장히 overloading해야 한다.

### 자바빈:

-   빈 생성자를 호출한 다음에, setter를 차례대로 호출해주는 방식
-   최종적인 인스턴스를 만들기까지 여러번의 호출을 거쳐야 하기 때문에 자바빈이 중간에 사용되는 경우 안정적이지 않은 상태로 사용될 여지가 있다.
-   (게터와 세터가 있어서) 불변 클래스(아이템 17)로 만들지 못한다.
-   (쓰레드 간에 공유 가능한 상태가 있으니까) 쓰레드 안정성을 보장하려면 추가적인 수고 (locking 같은) 가 필요하다.

### 빌더

-   빌더 패턴은 만들려는 객체를 바로 만들지 않고 클라이언트는 빌더(생성자 또는 static 팩토리)에 필수적인 매개변수를 주면서 호출해 Builder 객체를 얻은 다음 빌더 객체가 제공하는 세터와 비슷한 메소드를 사용해서 부가적인 필드를 채워넣고 최종적으로 build라는 메소드를 호출해서 만들려는 객체를 생성한다.

``` java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
.calories(100).sodium(35).carbohydrate(27).build();
```

-   빌더는 꽤 유연해서 빌더 하나로 여러 객체를 생성할 수도 있고 매번 생성하는 객체를 조금씩 변화를 줄 수도 있다. 만드는 객체에 시리얼 번호를 증가하는 식으로.
-   빌더 패턴은 클라이언트가 매개변수의 순서를 잘못 전달하는 것도 방지할 수 있다는 장점이 있다.
-   빌더는 생성할 클래스 안에 static member class로 만들어 두는게 보통이다.
-   [item2/builder/NutritionFacts.java](https://github.com/umbum/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item2/builder/NutritionFacts.java)
-   뭔가 잘못되었다면 보통 IllegalArgumentException을 던진다.
-   빌더 패턴은 상속을 통해 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
-   [item2/hierarchicalbuilder](https://github.com/umbum/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item2/hierarchicalbuilder)
    -   `Builder<T extends Builder<T>>` 로 하위 타입을 반환할 수 있도록 했는데, 이런 것을 공변 반환 타이핑(covariant return typing)이라고 한다.
    -   lombok의 @Builder를 많이 사용한다.
    -   빌더 애너테이션을 사용하면서 어떤 필드에 =으로 값을 초기화하는 경우 에러가 발생하므로, 초기화할 대상 필드에는 @Builder.Default를 붙여준다.
-   단점으로는 객체를 만들기 전에 먼저 빌더를 만들어야 하는데 성능에 민감한 상황에서는 그점이 문제가 될 수도 있다. 그리고 생성자를 사용하는 것보다 코드가 더 장황하다. 따라서 빌더 패턴은 매개변수가 많거나(4개 이상?) 또는 앞으로 늘어날 가능성이 있는 경우에 사용하는것이 좋다.