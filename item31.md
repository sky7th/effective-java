## item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

### 제네릭은 불공변
item 28에서 이야기했듯 매개변수화 타입은 불공변(invariant) 이다. 

즉, 서로 다른 타입 Type1과 Type2가 있을 때, List<Type1>은 List<Type2>의 하위 타입도 상위 타입도 아니다.

### 생산자(Producer)와 와일드카드
```java
// 와일드카드 타입을 사용하지 않은 pushAll 메서드 - 결함이 있다!
public void pushAll(Iterable<E> src) {
    for (E e : src) {
        push(e);
    }
}
```

위의 코드는 컴파일은 정상적으로 수행되지만 아래와 같이 Number 타입으로 선언된 Stack 객체의 메서드에 Integer 타입의 매개변수를 전달하면 컴파일 오류가 발생한다.

Integer는 Number의 하위 타입이니 정상적으로 잘 동작할 것 같지만 그렇지 않다.

**제네릭의 매개변수화 타입은 불공변이기 때문에 상위-하위 자료형의 관계가 없다.** 이러한 문제를 해결하기 위해 한정적 와일드카드(bounded wildcard) 자료형을 사용한다. 

Integer 클래스는 Number를 상속한 구현체 이므로 아래와 같이 매개변수 부분에 선언한다.

```java
// 생산자(producer) 매개변수에 와일드카드 타입 적용
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}
```

선언한 부분은 **매개변수는 E의 Iterable이 아니라 E의 하위 타입의 Iterable** 이라는 뜻이다.

이제 Number 클래스를 상속하는 Integer, Long, Double 등의 타입 요소를 가질 수 있게 된다.

### 소비자(Consumer)와 와일드카드

```java
// 와일드카드 타입을 사용하지 않은 popAll 메서드 - 결함이 있다!
public void popAll(Collection<E> dst) {
    while(!isEmpty()) {
        dst.add(pop());
    }
}
```
pushAll 함수와 유사하게 Collection의 요소 타입과 Stack의 요소 타입이 일치하면 오류는 발생하지 않으나, 위에서 작성한 예제처럼 타입이 일치하지 않으면 컴파일 에러가 발생한다.

```java
// 소비자(consumer) 매개변수에 와일드카드 타입 적용
public void popAll(Collection<? super E> dst) {
    while(!isEmpty()) {
        dst.add(pop());
    }
}
```

선언한 부분은 **매개변수는 E의 Collection이 아니라 E의 상위 타입인 Collection** 이라는 뜻이다.

이제 받을 수 있는 모든 타입은 자기 자신의 상위 타입이다.

### PECS
Producer-Extends-Consumer-Super의 약자이다.

**메서드의 매개변수 타입이 생산자를 나타내면 <? extends T>를 사용하고 소비자의 역할을 한다면 <? super T>를 사용하면 된다.**


### Advanced
**메서드의 리턴값에는 와일드카드 타입을 사용하면 안된다.**

왜냐하면 메서드를 사용하는 클라이언트 코드에서도 메서드 반환 값으로 와일드카드 자료형을 써야하기 때문이다.

재귀적 타입 한정(Recursive Type Bound)을 사용한 메서드를 살펴보자.

```java
// 변경 전
public static <E extends Comparable<E>> E max(Collection<E> collection)

// 변경 후(PECS 공식 2번 적용)
public static <E extends Comparable<? super E>> E max(Collection<? extends E> collection)
```

매개변수는 Producer 이므로 매개변수 선언 부분은 Collection<? extends E>가 되어야 한다. 

그리고 Comparable 은 E 인스턴스를 소비하는 Consumer이므로 super가 적용된다. 따라서 아래와 같이 PECS 공식을 2번 적용한 형태로 변경되어야 한다.

복잡하지만 위와 같은 방식은 Comparable을 예로 들었을 때, Comparable을 직접 구현하지 않고 직접 구현한 다른 클래스를 확장한 타입을 지원할 때 필요하다.

```java
List<ScheduledFuture<?>> scheduledFutures = ...;
```
위에서 수정 전 max는 이 리스트를 처리할 수 없다. 그 이유를 알기 위해 ScheduledFuture 인터페이스의 구현 코드를 살펴보자.

```java
public interface ScheduledFuture<V> extends Delayed, Future<V> 
public interface Delayed extends Comparable<Delayed>
public interface Comparable<T>
```

ScheduledFuture은 Delayed의 하위 인터페이스이며 Delayed는 Comparable<Delayed>를 확장했다.

반면에 ScheduledFuture은 Comparable<ScheduledFuture>를 확장하지 않았다.

ScheduledFuture의 인스턴스는 다른 ScheduledFuture 인스턴스뿐 아니라 Delayed 인스턴스와도 비교할 수 있어서 수정 전 max가 이 리스트를 거부하는 것이다.

C**omparable(혹은 Compatator)을 직접 구현하지 않고 직접 구현한 다른 타입을 확장한 타입을 지원하기 위해 와일드카드가 필요하다.**

와일드카드와 관련해 하나 더 살펴보자.

타입 매개변수와 와일드카드에는 공통되는 부분이 있어서, 메서드를 정의할 때 둘 중 어느 것을 사용해도 괜찮을 때가 많다.

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, in i, int j);
```

public API라면 간단한 두 번째가 낫다. 
- 어떤 리스트든 이 메서드에 넘기면 명시한 인덱스의 원소들을 교환해 줄 것이다. 
- 신경 써야 할 타입 매개변수도 없다.


메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라.
- 비한정적 타입 매개변수 -> 비한정적 와일드카드
- 한정적 타입 매개변수 -> 한정적 와일드카드

하지만 아래와 같은 코드에서 컴파일되지 않는다.

```java
public static void swap(List<?> list, in i, int j) {
  list.set(i, list.set(j, list.get(i)));
}
```

List<?>에는 null 외에는 어떤 값도 넣을 수 없다.

이 상황에서 형변환이나 리스트의 로 타입을 사용하지 않고도 해결할 길이 있다.

바로 **와일드카드 타입의 실제 타입을 알려주는 메서드를 private 도우미 메서드로 따로 작성**하여 활용하는 방법이다.

```java
public static void swap(List<?> list, in i, int j) {
  swapHelper(list, i, j);
}

// 와일드카드 타입을 실제 타입으로 바꿔주는 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j) {
  list.set(i, list.set(j, list.get(i)));
}
```

이 리스트에서 꺼낸 값의 타입은 항상 E이고, E 타입의 값이라면 이 리스트에 넣어도 안전함을 알고 있다.

---

조금 복잡하더라도 와일드카드 타입을 적용시키면 API가 훨씬 유연해진다. 그러니 널리 쓰일 라이브러리를 작성한다면 반드시 와일드카드 타입을 적절히 사용해줘야 한다. PECS 공식을 기억하자 !