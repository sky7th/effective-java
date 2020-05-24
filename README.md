## 객체 생성과 파괴
[item 1. 생성자 대신 static 팩토리 메소드를 고려하라](https://github.com/sky7th/effective-java/blob/master/item1.md)

[item 2. 생성자에 매개변수가 많다면 빌더를 고려하라](https://github.com/sky7th/effective-java/blob/master/item2.md)

[item 3. private 생성자나 enum 타입으로 싱글턴임을 보증하라](https://github.com/sky7th/effective-java/blob/master/item3.md)

[item 4. 인스턴스화를 막으려거든 private 생성자를 사용하라](https://github.com/sky7th/effective-java/blob/master/item4.md)

[item 5. 자원을 직접 명시하지 말고 의존 객체 주입(DI)을 사용하라](https://github.com/sky7th/effective-java/blob/master/item5.md)

[item 6. 불필요한 객체 생성을 피하라](https://github.com/sky7th/effective-java/blob/master/item6.md)

[item 7. 다 쓴 객체 참조를 해제하라.](https://github.com/sky7th/effective-java/blob/master/item7.md)

[item 8. finalizer와 cleaner 사용을 피하라](https://github.com/sky7th/effective-java/blob/master/item8.md)

[item 9. try-finally 보다는 try-with-resources를 사용하라.](https://github.com/sky7th/effective-java/blob/master/item9.md)  


## 모든 객체의 공통 메서드

[item 10. equals는 일반 규약을 지켜 재정의하라](https://github.com/sky7th/effective-java/blob/master/item10.md)

[item 11. equals를 재정의하려거든 hashCode도 재정의하라](https://github.com/sky7th/effective-java/blob/master/item11.md)

[item 12. toString을 항상 재정의하라](https://github.com/sky7th/effective-java/blob/master/item12.md)

## 클래스와 인터페이스

[item 15. 클래스와 멤버의 접근 권한을 최소화하라](https://github.com/sky7th/effective-java/blob/master/item15.md)

[item 16. public 클래스에서는 public 필드가 아닌 접근자 메스드를 사용하라](https://github.com/sky7th/effective-java/blob/master/item16.md)

[item 17. 변경 가능성을 최소화하라](https://github.com/sky7th/effective-java/blob/master/item17.md)

[item 18. 상속보다는 컴포지션을 사용하라](https://github.com/sky7th/effective-java/blob/master/item18.md)

[item 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라](https://github.com/sky7th/effective-java/blob/master/item19.md)

[item 20. 추상 클래스보다는 인터페이스를 우선하라](https://github.com/sky7th/effective-java/blob/master/item20.md)

[item 21. 인터페이스는 구현하는 쪽을 생각해 설계하라](https://github.com/sky7th/effective-java/blob/master/item21.md)

[item 22. 인터페이스는 타입을 정의하는 용도로만 사용하라](https://github.com/sky7th/effective-java/blob/master/item22.md)

[item 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라](https://github.com/sky7th/effective-java/blob/master/item23.md)

[item 24. 멤버 클래스는 되도록 static으로 만들라](https://github.com/sky7th/effective-java/blob/master/item24.md)

[item 25. 톱레벨 클래스는 한 파일에 하나만 담으라](https://github.com/sky7th/effective-java/blob/master/item25.md)

## 제네릭

[item 26. 로 타입은 사용하지 말라](https://github.com/sky7th/effective-java/blob/master/item26.md)

[item 27. 비검사 경고를 제거하라](https://github.com/sky7th/effective-java/blob/master/item27.md)

[item 28. 배열보다는 리스트를 사용하라](https://github.com/sky7th/effective-java/blob/master/item28.md)

[item 29. 이왕이면 제네릭 타입으로 만들라](https://github.com/sky7th/effective-java/blob/master/item29.md)

[item 30. 이왕이면 제너릭 메서드로 만들라](https://github.com/sky7th/effective-java/blob/master/item30.md)

[item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라](https://github.com/sky7th/effective-java/blob/master/item31.md)


## 메서드
[item 49. 매개변수가 유효한지 검사하라](https://github.com/sky7th/effective-java/blob/master/item49.md)

[item 50. 적시에 방어적 복사본을 만들라](https://github.com/sky7th/effective-java/blob/master/item50.md)

[item 51. 메서드 시그니처를 신중하게 설계하라](https://github.com/sky7th/effective-java/blob/master/item51.md)

[item 52. 다중정의는 신중히 사용하라](https://github.com/sky7th/effective-java/blob/master/item52.md)

[item 53. 가변인수는 신중히 사용하라](https://github.com/sky7th/effective-java/blob/master/item53.md)

[item 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라](https://github.com/sky7th/effective-java/blob/master/item54.md)

[item 55. 옵셔널 반환은 신중히 하라](https://github.com/sky7th/effective-java/blob/master/item55.md)

[item 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라](./item56.md)

## 일반적인 프로그래밍 원칙
[item 57. 지역변수의 범위를 최소화하라](./item57.md)

[item 58. 전통적인 for 문보다는 for-each 문을 사용하라](./item58.md)

[item 59. 라이브러리를 익히고 사용하라](./item59.md)

[item 60. 정확한 답이 필요하다면 float와 double은 피하라](./item60.md)

[item 61. 박싱된 기본 타입보다는 기본 타입을 사용하라](./item61.md)

[item 62. 다른 타입이 적절하다면 문자열 사용을 피하라](./item62.md)

[item 63. 문자열 연결은 느리니 주의하라](./item63.md)

[item 64. 객체는 인터페이스를 사용해 참조하라](./item64.md)