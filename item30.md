## item 30. 이왕이면 제너릭 메서드로 만들라

클래스와 마찬가지로, 메서드도 제네릭으로 만들 수 있다.

매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다.

타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 온다.

### 제네릭 싱글톤 팩터리
제네릭은 런타임에 타입 정보가 소거(item 28)되므로 하나의 객체를 어떤 타입으로든 매개변수화 할 수 있다. 

하지만 요청한 타입 매개변수에 맞도록 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 한다. 
(예: Collections.reverseOrder, Collections.emptySet)

### 재귀적 타입 한정
자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있다.

주로 타입의 자연적 순서를 정하는 Comparable 인터페이스(item 14)와 함께 쓰인다.

```java
// 재귀적 타입 한정을 이용해 상호 비교할 수 있음을 표현했다.
public static <E extends Comparable<E>> E max(Collection<E> c);
```
위의 코드에서 타입 한정인 ```<E extends Comparable<E>>```는 “모든 타입 E는 자신과 비교할 수 있다” 라고 읽을 수 있다.

**형변환 해야할 일이 많다면 경우 제네릭 메서드를 사용하자.**


### 정리가 잘 되어있는 글
[[ Java] Java의 Generics - Leopold Baik (백중원) - Medium](https://medium.com/@joongwon/java-java%EC%9D%98-generics-604b562530b3)