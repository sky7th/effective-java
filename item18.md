## item 18. 상속보다는 컴포지션을 사용하라

**상속은 잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만들게 된다.**
- 상위 클래스와 하위 클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 상속도 안전한 방법이다.
- 확장할 목적으로 설계되었고 문서화도 잘 된 클래스(item 19)도 마찬가지로 안전하다.
- 하지만 일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체 클래스를 상속하는 일은 위험하다.

메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.
- 상위 클래스를 릴리스마다 내부 구현이 달라질 수 있으며, **그 여파로 코드 한 줄 건드리지 않은 하위 클래스가 오동작할 수 있다.**

메서드 재정의로 인한 문제가 발생할 수 있다. 

그렇다면 클래스를 확장하더라도 메서드를 재정의하는 대신 새로운 메서드를 추가하면 괜찮을까? 
- 이 방식이 훨씬 안전한 것은 맞지만, 위험이 전혀 없는 것은 아니다.
- 다음 릴리스에서 상위 클래스에 새 메서드가 추가됐는데, 운 없게도 하필 내가 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입은 다르다면 컴파일 조차 되지 않는다.
- 상위 클래스의 메서드가 요구하는 규약을 만족하지 못할 가능성이 크다.

다행이 이상의 문제를 모두 피해 가는 묘안이 있다.

**기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하자.** 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션이라 한다.

새 클래스의 인스턴스 메서드들은 (private 필드를 참조하는) 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환한다.

새로운 클래스는 **기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.**

```java
// 래퍼 클래스 - 상속 대신 컴포지션을 사용했다.
public class MySet<E> extends ForwardingSet<E>  {
    private int addCount = 0;

    public MySet(Set<E> set) {
        super(set);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> collection) {
        addCount = addCount + collection.size();
        return super.addAll(collection);
    }

    public int getAddCount() {
        return addCount;
    }
}
// 재사용할 수 있는 전달 클래스
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> set;
    public ForwardingSet(Set<E> set) { this.set = set; }
    public void clear() { set.clear(); }
    public boolean isEmpty() { return set.isEmpbty(); }
    public boolean add(E e) { return set.add(e); }
    public boolean addAll(Collection<? extends E> c) { return set.addAll(c); }
    ... 
}
```

InstrumentedSet은 Set 인터페이스를 구현했고, Set의 인스턴스를 인수로 받는 생성자를 하나 제공한다. 임의의 Set에 계측 기능을 덧씌워 새로운 Set으로 만드는 것이 이 클래스의 핵심이다.

상속 방식은 구체 클래스 각각을 따로 확장해야 하며, 지원하고 싶은 상위 클래스의 생성자 각각에 대응하는 생성자를 별도로 정의해줘야 한다.

하지만 **컴포지션 방식은 한 번만 구현해두면 어떠한 Set 구현체라도 계측**할 수 있으며, **기존 생성자들과도 함께 사용**할 수 있다.

```java
Set<Instant> times = new InstrumentedSet<>(new TreeSet<>(cmp));
Set<E> s = new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
```

InstrumnetedSet을 이용하면 대상 Set 인스턴스를 특정 조건하에서만 임시로 계측할 수 있다.

```java
static void walk(Set<Dog> dogs) {
  InstrumentedSet<Dog> iDogs = new InstrumentedSet<>(dogs);
  ... // 이 메서드에서는 dogs 대신 iDogs를 사용한다.
}
```

다른 Set 인스턴스를 감싸고 있다는 뜻에서 InstrumentedSet 같은 클래스를 **래퍼 클래스**라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 **데코레이터 패턴**이라고 한다.

**컴포지션과 전달의 조합은 넓은 의미로 위임(deligation)이라고 부른다.** 단, 엄밀히 따지면 래퍼 객체가 내부 객체에 자기 자신의 참조를 넘기는 경우만 위임에 해당한다.

**래퍼 클래스는 콜백 프레임워크와는 어울리지 않는다.**

콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출(콜백) 때 사용하도록 한다. 내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 된다.(SELF 문제)

**재사용할 수 있는 전달 클래스를 인터페이스당 하나씩만 만들어두면 원하는 기능을 덧씌우는 전달 클래스들을 아주 손쉽게 구현할 수 있다.**

상속은 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 쓰여야 한다. 다르게 말하면, **클래스 B가 클래스 A와 is-a 관계일 때만 클래스 A를 상속해야 한다.** 클래스 A를 상속하는 클래스 B를 작성하려 한다면 "B가 정말 A인가?"하고 자문해보자. 대답이 "아니다"라면 A를 private 인스턴스로 두고, A와는 다른 API를 제공해야 하는 상황이 대다수다. 즉, A는 B의 필수 구성요소가 아니라 구현하는 방법 중 하나일 뿐이다.

**컴포지션을 써야 할 상황에서 상속을 사용하는 건 내부 구현을 불필요하게 노출하는 꼴이다.**
- 클라이언트가 노출된 내부에 직접 접근할 수 있어서 문제가 된다.

컴포지션 대신 상속을 사용하기로 결정하기 전에 마지막으로 자문해야 할 질문들
- 확장하려는 클래스의 API에 아무런 결함이 없는가?
- 결함이 있다면, 이 결함이 여러분 클래스의 API까지 전파돼도 괜찮은가?


컴포지션으로는 이런 결함을 숨기는 새로운 API를 설계할 수 있지만, **상속은 상위 클래스의 API를 그 결함까지도 그래도 승계한다.**

---

하위 클래스의 패키지가 상위 클래스와 다르고, 상위 클래스가 확장을 고려해 설계되지 않았다면 문제가 될 수 있다. 

상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자. 특히 래퍼 클래스로 구현할 적당한 인터페이스가 있다면 더욱 그렇다. 

래퍼 클래스는 하위 클래스보다 견고하고 강력하다.