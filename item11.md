## 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라

equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 한다.

hashCode 재정의를 잘못했을 때 크게 문제가 되는 조항은 두 번째다. 즉, 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.

좋은 해시 함수라면 서로 다른 인스턴스에 다른 해시코드를 반환한다.

성능을 높인답시고 해시코드를 계산할 때 핵심 필드를 생략해서는 안된다.

AutoValue 프레임워크를 사용하면 멋진 equals와 hashCode를 자동으로 만들어준다.

#### 전형적인 hashCode 메서드

```java
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```

#### 해시코드를 지연 초기화하는 hashCode 메서드 - 스레드 안전성까지 고려해야 한다.

```java
private int hashCode; // 자동으로 0으로 초기화된다.

@Override public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
        hashCode = result;
    }
    return result;
}
```