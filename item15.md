
## item 15. 클래스와 멤버의 접근 권한을 최소화하라

잘 설계된 컴포넌트는 모든 내부 구현을 완벽히 숨겨, 구현과 API를 깔끔히 분리한다.

프로그램 요소의 접근성은 가능한 한 최소한으로 하라.

#### public 클래스는 상수용 public static final 필드 외에는 어떠한 public 필드도 가져서는 안 된다. public static final 필드가 참조하는 객체가 불변인지 확인하자.

public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.

#### 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안 된다. (내용을 수정할 수 있게 된다)

```java
// 보안 허점이 숨어 있다.
public static final Thing[] VALUES = { ... };
```

해결책 1. public 불변 리스트 추가
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

해결책 2. 복사본을 반환하는 public 메서드 추가
``` java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> values() {
  return PRIVATE_VALUES.clone();
}
```

#### java 9 에서는 모듈 시스템이라는 개념이 도입되었다.
- 모듈은 패키지들의 묶음
- 모듈은 자신에 속하는 패키지 중 공개(export)할 것들을 (관례상 module-info.java 파일에) 선언한다.
- protected 혹은 public 멤버라도 해당 패키지를 공개하지 않았다면 모듈 외부에서는 접근할 수 없다.
- 모듈 jar 파일을 자신의 모듈 경로가 아닌 애플리케이션의 classpath에 두면 그 모듈 안의 모든 패키지는 마치 모듈이 없는 것처럼 행동한다.(공개 여부와 상관없이 모듈 밖에서 접근할 수 있게 된다)