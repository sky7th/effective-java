## 아이템 1. 생성자 대신 static 팩토리 메소드를 고려하라

``` java
// 예시 코드 1
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
} 
```

클래스는 클라이언트에 public 생성자 대신 (혹은 생성자와 함께) 정적 팩터리 메서드를 제공할 수 있다.

### 장점 1. 이름을 가질 수 있다.

-   생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 제대로 설명하지 못한다. 반면 정적 팩터리는 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있다.
-   생성자는 시그니처에 제약이 있다. 똑같은 타입을 파라미터로 받는 생성자 두개를 만들 수 없기 때문에 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주자.

### 장점 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

-   매번 새로운 객체를 만들 필요가 없는 경우에 미리 만들어둔 인스턴스 또는 캐시해둔 인스턴스를 반환할 수 있다.
-   (예시 코드 1)에서 Boolean.valueOf(boolean) 메서드는 객체를 아예 생성하지 않는다.

### 장점 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

-   리턴 타입은 interface로 하고, 실제 리턴은 그를 구현한 class의 인스턴스를 반환해주는 것으로 하면 구현 class의 상세를 숨길 수 있다.
-   API를 쓰는 사람으로 하여금 interface로 접근 하도록 유도할 수도 있고, 구현 class들을 숨기게 되므로 API 명세도 훨씬 간단해진다.
-   java.util.Collections는 45개에 달하는 인터페이스의 구현체의 인스턴스를 제공하지만 그 구현체들은 전부 non-public으로 인터페이스 뒤에 감춰서 public으로 제공해야 할 API를 줄였다.

### 장점 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

-   API를 사용하는 클라이언트에서는 어떤 클래스가 반환되는지 관심 없고, 인터페이스로 접근하기 때문에 다음 릴리즈에서는 다른 클래스의 객체를 반환해도 된다.
-   의존성이 낮아진다.

### 장점 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

-   장점 3, 4와 비슷한 개념이다. 이러한 유연성을 제공하는 static 팩토리 메소드는 서비스 프로바이더 프레임워크의 근본이다.
-   대표적인 서비스 제공자 프레임워크로는 JDBC가 있다.
-   interface로 다루면 클래스가 없어도 되니까. DI 받을 때 interface로 받는 그런 맥락.

### 단점 1. 상속을 하려면 public 이나 protected 생성자가 필요한데 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

-   불변 타입(아이템 17)인 경우나 상속 대신 컴포지션을 권장(아이템 18)하기 때문에 장점으로 볼 수도 있다.

### 단점 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

-   생성자는 명확한 반면 static factory는 좀 덜 명확하기 때문에 메서드명을 컨벤션을 따라서 잘 지어줘야 한다.
    
    ``` java
    // from : ~로 부터 반환. 형변환해서 반환.
    Date d = Date.from(instant);
    // of : 집계해서 반환.
    Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
    // valueOf : from과 of의 더 자세한 버전
    BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
    // instance 혹은 getInstance : 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않음.
    StackWalker luke = StackWalker.getInstance(options);
    // get{Type} : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때.
    FileStore fs = Files.getFileStore(path);
    // new{Type} : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때.
    BufferedReader br = Files.newBufferedReader(path);
    // {type} : get{Type}과 new{Type}의 간결한 버전
    List<Complaint> litany = Collections.list(legacyLitany);
    ```
    
-   생성자는 Javadoc 상단에 모아서 보여주지만 static 팩토리 메소드는 API 문서에서 특별히 다뤄주지 않는다. 따라서 클래스나 인터페이스 문서 상단에 팩토리 메소드에 대한 문서를 제공하는 것이 좋겠다.