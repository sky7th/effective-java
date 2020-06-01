## item 62. 다른 타입이 적절하다면 문자열 사용을 피하라

더 적합한 데이터 타입이 있거나 새로 작성할 수 있다면, 문자열을 쓰고 싶은 유혹을 뿌리쳐라.

문자열은 잘못 사용하면 번거롭고, 덜 유연하고, 느리고, 오류 가능성도 크다.

문자열을 잘못 사용하는 흔한 예로는 기본 타입, 열거 타입, 혼합 타입이 있다.

#### 문자열은 다른 값을 대신하기 적절하지 않다.
- 숫자를 표현할때는 int, float 등등의 타입이 좋고 예/아니오는 boolean 타입이 좋다
#### 문자열은 열거타입을 대신하기에 적합하지 않다.
- 상수를 열거할 때는 문자열보다는 열거 타입이 월등히 낫다. (item 34)
#### 문자열은 혼합타입을 대신하기 적합하지 않다.
- ```java
  // 문자열로 혼합 타입을 작성
  String compoundKey = className + "#" + i.next();
  ```
  구분을 #으로만 하고 두 타입 중에 하나라도 #이 쓰이면 문제가 된다. 그리고 타입을 나눌 때는 파싱을 해야 되기 때문에 느리다.
#### 문자열은 권한을 표현하기에 적합하지 않다.
에를 들어 스레드 지역변수 기능을 설계한다고 해보자. 그 이름처럼 각 스레드가 자신만의 변수를 갖게 해주는 기능이다.

```java
// 잘못된 예 - 문자열을 사용해 권한을 구분하였다.
public class ThreadLocal {
    private ThreadLocal() { } // 객체 생성 불가

    // 현 스레드의 값을 키로 구분해 저장한다.
    public static void set(String key, Object value);

    // (키가 가르키는) 현 스레드의 값을 반환한다.
    public static Object get(String key);
}
```
- 각 클라이언트가 고유한 키를 제공해야 하지만 문제점이 존재한다.
- 이 방식의 문제는 스레드 구분용 문자열 키가 전역 이름공간에서 공유된다는 점이다.

이 API는 문자열 대신 위조할 수 없는 키를 사용하면 해결된다. 이 키를 권한(capacity)이라고도 한다.

```java
// Key 클래스로 권한을 구분했다.
public class ThreadLocal {
    private ThreadLocal() { } // 객체 생성 불가

    public static class Key { // (권한)
        Key() { }
    }

    // 위조 불가능한 고유 키를 생성한다.
    public static Key getKey() {
        return new Key();
    }

    public static void set(Key key, Object value);
    public static Object get(Key key);
}
```
- 이 방법은 위의 문제를 해결해주지만 개선할 여지가 있다.
- set과 get은 이제 정적 메서드일 이유가 없으니 Key클래스의 인스턴스 메서드로 바꾸자.
- 이렇게 하면 Key는 더 이상 스레드 지역변수를 구분하기 위한 키가 아니라, 그 자체가 스레드 지역변수가 된다.
- 중첩 클래스 Key의 이름을 ThreadLocal로 바꿔버리자.

```java
// 리팩터링하여 Key를 ThreadLocal로 변경
public final class ThreadLocal {
    public ThreadLocal();
    public static void set(Object value);
    public static Object get();
}
```
- 이 API에서는 get으로 얻은 Object를 실제 타입으로 형변환해 써야 해서 타입 안전하지 않다.
- ThreadLocal을 매개변수화 타입(item 29)으로 선언하면 간단하게 문제가 해결된다.

```java
// 매개변수화하여 타입안전성 확보
public final class ThreadLocal<T> {
    public ThreadLocal();
    public static void set(T value);
    public static T get();
}
```
- 이제 자바의 java.lang.ThreadLocal과 흡사해졌다.
- 문자열 기반 API의 문제를 해결해주며, 키 기반 API보다 빠르고 우아하다.