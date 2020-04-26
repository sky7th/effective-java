## 아이템 8. finalizer와 cleaner 사용을 피하라
​
자바는 finalizer와 cleaner 두 가지 객체 소멸자를 제공한다.
​
그중 finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다.
​
그 대안으로 소개된 cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.
​
-   객체에 접근 할 수 없게 된 후 finalizer와 cleaner가 실행되기까지 얼마나 걸릴지 알 수 없기 때문에 이들로는 제때 실행되어야 하는 작업은 절대 할 수 없다.
-   finalizer나 cleaner를 얼마나 신속히 수행할지는 가비지 컬렉터 구현마다 천자만별이다.
    -   테스트한 JVM에서는 완벽하게 동작하던 프로그램이 고객의 시스템에서는 엄청난 문제가 생길 수도 있다.
-   상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안 된다.
    -   데이터베이스 같은 공유 자원의 영구 락 해제를 finalizer나 cleaner에 맡겨 놓으면 분산 시스템 전체가 서서히 멈출 것이다.
-   finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수도 있다.
-   final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언하자.
-   파일이나 스레드 등 종료해야 할 자원을 담고 있는 객체의 클래스에서 finalizer나 cleaner 대신 AutoCloseable을 구현해주고, 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 된다. (try-with-resources를 사용해야 한다. 아이템 9)
-   cleaner는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 물론 리얼ㄴ 경우라도 불확실성과 성능 저하에 주의해야 한다.