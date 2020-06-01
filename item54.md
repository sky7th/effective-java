## item 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라

컬렉션이나 배열 같은 컨테이너가 비었을 때 null을 반환하는 메서드를 사용할 때면 항시 방어 코드를 넣어줘야 한다.

클라이언트에서 방어 코드를 빼먹으면 오류가 발생할 수 있다.

null을 반환하려면 반환하는 쪽에서도 이 상황을 특별히 취급해줘야 해서 코드가 더 복잡해진다.

#### null이 아닌 빈 배열이나 컬렉션을 반환하라. 
- null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어난다. 
- 그렇다고 성능이 좋은 것도 아니다.

#### 빈 컨테이너를 할당하는 데 비용이 든다?
- 이 할당이 성능 저하의 주범이라고 확인되지 않는 한(item 67), 신경 안 써도 된다.
- 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있다.

### null 반환 - 따라 하지 말 것!
```java
// 컬렉션이 비었으면 null을 반환한다. -따라 하지 말 것!
private final List<Cheese> cheesesInStock = ...;

public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? null
        : new ArrayList<>(cheesesInStock);
}
```

### 빈 컬렉션 반환
```java
// 빈 컬렉션을 반환하는 올바른 예
public List<Cheese> getCheeses() {
    return new ArrayList<>(cheesesInStock);
}
```

```java
// 최적화 - 빈 컬렉션을 매번 새로 할당하지 않도록 했다.
// 매번 똑같은 빈 '불변' 컬렉션을 반환한다.
public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? Collections.emptyList()
        : new ArrayList<>(cheesesInStock);
}
```
### 길이가 0일 수도 있는 배열 반환

```java
// 길이가 0일 수도 있는 배열을 반환하는 올바른 방법
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```
```java 
// 최적화 - 빈 배열을 매번 새로 할당하지 않도록 했다.
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```
- cheesesInStock이 비었을 때면 언제나 EMPTY_CHEESE_ARRAY를 반환한다.

단순히 성능을 개선할 목적이라면 toArray에 넘기는 배열을 미리 할당하는 건 추천하지 않는다. 오히려 성능이 떨어진다는 연구 결과도 있다.

```java
// 나쁜 예 = 배열을 미리 할당하면 성능이 나빠진다.
return cheesesInStock.toArray(new Cheese[cheesesInStock.size()]);
```