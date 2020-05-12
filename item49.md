## item 49. 매개변수가 유효한지 검사하라

메서드 몸체가 실행되기 전에 매개변수를 확인한다면 잘못된 값이 넘어왔을때 즉각적이고 깔끔한 방식으로 예외를 던질 수 있다.

**매개변수 검사를 제대로 하지 못하면 생길 수 있는 문제**
- 메서드가 수행되는 중간에 모호한 예외를 던지며 실패할 수 있다.
- 메서드가 잘 수행되지만 잘못된 결과를 반환할 수 있다.
- 메서드는 문제없이 수행됐지만, 어떤 객체를 이상한 상태로 만들어놓아서 미래의 알 수 없는 시점에 이 메서드와는  관련 없는 오류를 낼 수도 있다.


**public과 protected 메서드는 매개변수 값이 잘못됐을 때 던지는 예외를 문서화해야 한다.**

클래스 수준 주석은 그 클래스의 모든 public 메서드에 적용되므로 훨씬 깔끔하다. 
 
@Nullable과 같은 어노테이션을 사용할 수도 있지만 표준은 아니다. 

**더불어 생성자 매개변수 검사도 클래스 불변식을 어기는 객체가 생성되지 않게 하기 위하여 꼭 필요하다.**


### assert
- private으로 공개되지 않은 메서드라면 개발자가 직접 호출되는 상황을 통제할 수 있다. 
- 이럴 때는 assert를 사용하여 매개변수 유효성을 검사할 수 있다. 
- 실행시에 assert를 수행하려면 인텔리제이 기준으로 VM Options에 -ea 또는 --enableassertions 를 넘겨주어야 한다. 

- 값을 넘겨주지 않으면 무시된다. 
- 넘어온 매개변수가 조건식을 참으로 만들지 않으면 AssertionError를 던진다.

    ```java
    // 재귀 정렬용 private 도우미 함수
    private void sort(long a[], int offset, int length) {
        assert a != null;
        assert offset >= 0 && offset <= a.length;
        assert length >= 0 && length <= a.length - offset;
        ... // 계산 수행
    }
    ```

### 유효성 검사가 필요 없는 경우
- 유효성을 검사하는 비용이 지나치게 큰 경우 또는 계산 과정에서 암묵적으로 유효성 검사가 진행될 때