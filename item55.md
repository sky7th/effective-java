## item 55. 옵셔널 반환은 신중히 하라

### 메서드가 특정 조건에서 값을 반환할 수 없을 때
#### 자바 8 이전
- 예외를 던짐
  - 예외는 진짜 예외적인 상황에서만 사용해야 한다.(item 69) 
  - 예외를 생성할 때 스택 추적 전체를 캡처하므로 비용이 만만치 않다.
- null을 반환
  - 별도의 null 처리 코드를 추가해야 한다.
  - 언젠가 NullPointerException이 발생할 수 있다.

#### 자바 8 이후
- Optional\<T> 사용

### Optional
- null이 아닌 T 타입 참조를 하나 담거나, 혹은 아무것도 담지 않을 수 있다.
- 원소를 최대 1개 가질 수 있는 '불변'컬렉션이다.

### Optional 메서드
- #### Optional.empty()
  - 내부 값이 비어있는 Optional 객체 반환
- #### Optional.of(T value)
  - 내부 값이 value인 Optional 객체 반환
  - 만약 value가 null인 경우 NullPointerException 발생
- #### Optional.ofNullable(T value)
  - 가장 자주 쓰이는 Optional 생성 방법
  - value가 null이면, empty Optional을 반환하고, 값이 있으면 Optional.of로 생성
- #### T get()
  - Optional 내의 값을 반환
  - 만약 Optional 내부 값이 null인 경우 NoSuchElementException 발생
- #### boolean isPresent()
  - Optional 내부 값이 null이면 false, 있으면 true
  - Optional 내부에서만 사용해야하는 메서드라고 생각
- #### boolean isEmpty()
  - Optional 내부의 값이 null이면 true, 있으면 false
  - isPresent() 메서드의 반대되는 메서드
- #### void ifPresent(Consumer\<? super T> consumer)
  - Optional 내부의 값이 있는 경우 consumer 함수를 실행
- #### Optional\<T> filter(Predicate\<T> predicate)
  - Optional에 filter 조건을 걸어 조건에 맞을 때만 Optional 내부 값이 있음
  - 조건이 맞지 않으면 Optional.empty를 리턴
- #### Optional\<U> map(Funtion\<? super T, ? extends U> f)
  - Optional 내부의 값을 Function을 통해 가공
- #### T orElse(T other)
  - Optional 내부의 값이 있는 경우 그 값을 반환
  - Optional 내부의 값이 null인 경우 other을 반환
- #### T orElseGet(Supplier\<? extends T> supplier)
  - Optional 내부의 값이 있는 경우 그 값을 반환
  - Optional 내부의 값이 null인 경우 supplier을 실행한 값을 반환
- #### T orElseThrow()
  - Optional 내부의 값이 있는 경우 그 값을 반환
  - Optional 내부의 값이 null인 경우 NoSuchElementException 발생
- #### T orElseThrow(Supplier\<? extends X> exceptionSupplier)
  - Optional 내부의 값이 있는 경우 그 값을 반환
  - Optional 내부의 값이 null인 경우 exceptionSupplier을 실행하여 Exception 발생

### Optional을 사용하지 않은 예제
```java
// 컬렉션에서 최댓값을 구한다(컬렉션이 비었으면 예외를 던진다).
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty()) {
        throw new IllegalArgumentException("빈 컬렉션");
    }
    
    E result = null;
    for (E e : c) {
        if(result == null || e.compareTo(result) > 0) {
            result = Objects.requiredNonNull(e);
        }
    }
    
    return result;
}
```

### Optional을 사용한 예제
```java
// 컬렉션에서 최댓값을 구해 Optional<E>로 반환한다.
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    if (c.isEmpty()) {
        return Optional.empty();
    }
    
    E result = null;
    for (E e : c) {
        if (result == null || e.compareTo(result) > 0) {
            result = Objects.requiredNonNull(e);
        }
    }
    
    return Optional.of(result);
}
```

- Optional을 반환하여 client에서 더욱 더 유연하게 로직을 작성할 수 있다.
- Optional.of에 null을 넣으면 NullPointerException 이 발생하니 주의해야 한다.
- Optional을 리턴하는 메서드에서는 null을 리턴해서는 안된다. (Optional의 취지와 맞지 않기 때문)

### Optional을 사용해야 하는 이유
- Optional은 검사 예외와 취지가 비슷하다. 즉, 반환값이 있을 수도 있고, 없을 수도 있음을 API 사용자에게 명확히 알려준다.


#### Optional 활용1 - 기본값(defalut)를 정해둘 수 있다.
```java
// 실제로 예외를 던진 것이 아니라, empty Optional이 리턴되기 때문에 예외 생성 비용이 들지 않는다.
String lastWordInLexicon = max(words).orElse("단어 없음..");
```

#### Optional 활용2 - 원하는 예외를 던질 수 있다.
```java
// 값이 없는 경우 원하는 예외를 던질 수 있다.
Toy myToy = max(toys).orElseThrow(TemperTantrumException::new);
```

#### Optional 활용3 - 항상 값이 채워져 있는 경우
```java
// 값이 없는 경우에는 NoSuchElementException이 발생하니 반드시 값이 있는 경우에만 사용해야 한다.
Element lastNobleGas = max(Elements.NOBLE_GASES).get();
```

#### Optional 활용4 - 기본값을 설정하는 비용이 큰 경우
```java
// 기본값을 설정하는 비용이 아주 커서 부담이 되는 경우 orElseGet을 사용하면,
// 값이 처음 필요할 때 Supplier를 사용해 생성하므로 초기 생성비용을 낮출 수 있다.
Element lastNobleGas = max(Elements.NOBLE_GASES).get();
```

### Optional 안티 패턴
#### Collection, Stream, 배열은 Optional로 감싸지 말자
- Optional<List<T>>를 반환하기 보다는 빈 ArrayList를 반환하는 것이 좋다. 그렇게 하면 클라이언트 코드에서 Optional 처리 코드를 넣지 않아도 된다.

#### Optional을 Map의 키나 값으로 사용하지 말자
- Optional은 Collection의 키, 값, 원소나 배열의 원소로 사용하는게 적절한 상황은 거의 없다.

#### isPresent()를 사용하지 말자
```java
Optional.ofNullable(school).map(School::getClassRoom)    //Optional<School>
                           .map(ClassRoom::getTeacher)   //Optional<ClassRoom>
                           .map(Teacher::getSubject)     //Optional<Teacher>
                           .map(Subject::getSubjectName) //Optional<Subject>
                           .orElse(null);
```
- 반드시 이런식으로 사용하자.


### 추가 내용
- 박싱된 기본타입을 사용할 때에는 OptionalInt, OptionalDouble, OptionalLong을 사용하자
  - 박싱된 기본타입을 담는 Optional은 기본타입 보다 무거울 수 밖에 없다.
  - 따라서 OptionalInt, OptionalDouble, OptionalLong을 사용하는 것이 조금 더 낫다.
- 값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야 하는 메서드라면
Optional을 반환해야 하는 상황일 수 있다.
- Optional을 반환값 이외의 용도로 쓰는 경우는 거의 없다.


### 출처
[https://jaehun2841.github.io/2019/02/24/effective-java-item55/#optional-%EC%9D%B4%EB%9E%80](https://jaehun2841.github.io/2019/02/24/effective-java-item55/#optional-%EC%9D%B4%EB%9E%80)