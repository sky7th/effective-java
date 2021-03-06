## 아이템 10. equals는 일반 규약을 지켜 재정의하라
​
equals 메서드는 재정의하기 쉬워 보이지만 곳곳에 함정이 도사리고 있어서 자칫하면 끔찍한 결과를 초래한다.  
문제를 회피하는 가장 쉬운 방법은 아예 재정의하지 않는 것이다.
​
### 재정의 하지 말아야 하는 상황들
-   각 인스턴스가 본질적으로 고유하다. 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기에 해당한다.(예: Thread)
    
-   인스턴스의 '논리적 동치성'을 검사할 일이 없다.
    
-   상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
    
-   클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다. equals가 실수로라도 호출되는 걸 막고 싶다면 아래처럼 구현하자.
    
    ```java
    @Override public boolean equals(Object o) {
      throw new AssertionError(); // 호출 금지!
    }
    ```

### 재정의 해야 하는 상황들
-   논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때
    -   두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶어 할 것이다.
-   재정의할 때 반드시 따라야 하는 일반 규약들
    -   반사성, 대칭성, 추이성, 일관성, null-아님
​
### equals 메서드 구현 방법 단계
1.  \== 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2.  instanceof 연산자로 입력이 올바른 타입인지 확인한다.
    -   인터페이스를 구현한 클래스라면 equals에서 (클래스가 아닌) 해당 인터페이스를 사용해야 한다.
3.  입력을 올바른 타입으로 형변환 한다. 앞서 2번 검사를 해서 100% 성공한다.
4.  입력 객체와 자기 자신의 대응되는 '핵심'필드들이 모두 일치하는지 하나씩 검사한다.
    -   모든 필드가 일치하면 true, 하나라도 다르면 false를 반환한다.
    -   2번에서 인터페이스를 사용했다면 입력의 필드 값을 가져올 때도 그 인터페이스의 메서드를 사용해야 한다.
    -   타입별 비교 방법
        -   float와 double을 제외한 기본 타입 필드: == 연산자로 비교
        -   float와 double 필드: 각각 정적 메서드인 Float.compare(float, float)와 Double.compare(double, double)로 비교
            -   float과 double을 특별 취급하는 이유는 Float.NaN, -0.0f, 특수한 부동소수 값 등을 다뤄야 하기 떄문이다.
            -   Float.equals 메서드는 오토박싱을 수반할 수 있어서 성능상 좋지 않다.
        -   참조 타입 필드: 각각의 equals 메서드로 비교
        -   배열의 모든 원소가 핵심 필드라면 Arrays.equals 메서드들 중 하나를 사용
        -   비교하기 아주 복잡한 필드: 그 필드의 표준형(canonical form)을 저장해둔 후 표준형끼리 비교하면 훨씬 경제적이다. (불변 클래스에 제격)
    -   다를 가능성이 더 크거나 비교하는 비용이 싼 필드를 먼저 비교하자.
5.  equals를 다 구현했다면 대칭적인지, 추이성이 있는지, 일관적인지 자문해보자.


### 주의사항
-   equals를 재정의할 땐 hashCode도 반드시 재정의하자(아이템 11).
-   너무 복잡하게 해결하려 들지 말자.
    -   필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있다. 일반적으로 별칭(alias)은 비교하지 않는 게 좋다.
-   equals 메서드는 Object 타입을 매개변수로 받도록 선언하자.
​
equals(hashCode도 마찬가지)를 작성하고 테스트하는 일은 지루하다. 다행이도 구글이 만듬 AutoValue 프레임워크가 있다. 클래스에 애너테이션 하나만 추가하면 AutoValue가 이 메서드들을 알아서 작성해준다.
​
대다수의 IDE도 같은 기능을 제공하지만 생성된 코드가 AutoValue만큼 깔끔하거나 읽기 좋지는 않다. 그래도 사람이 직접 작성하는 것보다는 IDE에 맡기는 편이 낫다.
​
### 핵심 정리
꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 대부분의 경우에 Object의 equals가 원하는 비교를 정확히 수행해준다. 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜나며 비교해야 한다.
​