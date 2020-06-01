## 아이템 3. private 생성자나 enum 타입으로 싱글턴임을 보증하라

싱글톤: 오직 한 인스턴스만 만드는 클래스

-   함수 같은 Stateless 객체(아이템 24) 또는 본질적으로 유일한 시스템 컴포넌트에 사용
-   싱글톤을 사용하는 클라이언트 코드를 테스트 하는게 어렵다. 싱글톤이 인터페이스를 구현한게 아니라면 mock으로 교체하는게 어렵기 때문이다.

### public static final 필드 방식의 싱글톤

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    ...
}
```

-   리플렉션을 사용해서 private 생성자를 호출할 수도 있는 단점이 있다.
    -   이 방법을 막고자 생성자 안에서 카운팅하거나 flag를 이용해서 예외를 던지게 할 수도 있다.
-   이런 API 사용이 정적 팩토리 메소드를 사용하는 방법에 비해 더 명확하고 더 간단하다.

### 정적 팩터리 방식의 싱글턴

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public static Elvis getInstance() {
        return INSTANCE;
    }
    ...
}
```

-   API를 변경하지 않고 처음엔 싱글톤으로 쓰다가 나중엔 쓰레드당 새 인스턴스를 만드는 등 클라이언트 코드를 고치지 않고도 변경할 수 있다.
-   필요하다면 Generic 싱글톤 팩토리(아이템 30)를 만들 수도 있다.
-   정적 팩토리 메소드를 `Supplier<Elvis>`에 대한 메소드 레퍼런스로 사용할 수도 있다.
    -   [자바 람다식 java Consumer , Supplier, Function 함수적 인터페이스](https://altongmon.tistory.com/245)

### 직렬화 (Serialization)

위에서 살펴본 두 방법 모두, 직렬화에 사용한다면 역직렬화 할 때 같은 타입의 인스턴스가 여러개 생길 수 있다. 그 문제를 해결하려면 모든 인스턴스 필드에 transient를 추가 (직렬화 하지 않겠다는 뜻) 하고 readResolve 메소드를 다음과 같이 구현하면 된다. ([객체 직렬화 API의 비밀)](https://www.oracle.com/technical-resources/articles/java/javaserial.html)

```java
private Object readResolve() {
    return INSTANCE;
}
```

### enum 타입 방식의 싱글턴 - 바람직한 방법

```java
public enum Elvis {
    INSTANCE;
    ...
}
```

-   직렬화/역직렬화 할 때 코딩으로 문제를 해결할 필요도 없고, 리플렉션으로 호출되는 문제도 고민할 필요없는 방법이다.
-   이 방법은 Enum 말고 다른 상위 클래스를 상속해야 한다면 사용할 수 없다. (인터페이스는 구현할 수 있다)