## item 63. 문자열 연결은 느리니 주의하라

성능에 신경 써야 한다면 많은 문자열을 연결할 때는 문자열 연결 연산자(+)를 피하자.

문자열 연결 연산자로 문자열 n개를 연결하는 시간은 n^2에 비례한다.

문자열은 불변(item 17)이라서 두 문자열을 연결할 경우 양쪽의 내용을 모두 복사해야 하므로 성능 저하가 생긴다.

##### 문자열 연결을 잘못 사용한 예 - 느리다!
```java
public String statement() {
    String result = "";
    for (int i = 0; i < numItems(); i++) {
        result += lineForItem(i); //문자열 연결
    }
    return result;
}
```
##### StringBuilder를 사용하면 문자열 연결 성능이 크게 개선된다.
```java
public String statement2() {
    StringBuilder sb = new StringBuilder(numItems() * LINE_WIDTH);
    for (int i = 0; i < numItems(); i++) {
       sb.append(lineForItem(i));
    }
    return sb.toString();
}
```
- StringBuilder를 전체 결과를 담기에 충분한 크기로 초기화한 점을 잊지 말자.