## item 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라


```java
// 태그 달린 클래스 - 클래스 계층구조보다 훨씬 나쁘다 !!
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
  // 태그 필드 - 현재 모양을 나타낸다.
    final Shape shape; 

    // 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
    double length;
    double width;

    // 다음 필드느 모양이 원(CIRCLE)일 때만 쓰인다.
    double radius;

    // 원용 생성자
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // 사각형용 생성자
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```

태그 달린 클래스의 단점
- 장황하고, 오류를 내기 쉽고, 비효율적이다.
- 클래스 계층구조를 어설프게 흉내낸 아류일 뿐이다.

**위의 태그 달린 클래스를 클래스 계층구조로 바꿔보자.**

가장 먼저 계층구조의 root가 될 추상 클래스를 정의하고, 태그 값에 따라 동작이 달라지는 메서드들을 root 클래스의 추상 메서드로 선언한다.

그런 다음 태그 값에 상관없이 동작이 일정한 메서드들을 root 클래스에 일반 메서드로 추가한다.

모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 전부 root 클래스로 올린다.

root 클래스를 확장한 구체 클래스를 의미별로 하나씩 정의한다.

```java
// 태그 달린 클래스를 클래스 계층구조로 변환
abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;
    Circle(double radius) { this.radius = radius; }
    @Override double area() { return Math.PI * (radius * radius); }
}

class Rectangle extends Figure {
    final double length;
    final double width;
    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
    @Override double area() { return length * width; }
}
```

**클래스 계층구조는 간결하고 명확하며, 쓸데없는 코드도 모두 사라졌다.**

살아 남은 필드들은 모두 final이다.

각 클래스의 생성자가 모든 필드를 남김없이 초기화하고 추상 메서드를 모두 구현했는지 컴파일러가 확인해준다.

또한, 타입 사이의 자연스러운 계층 관계를 반영할 수 있어서 유연성은 물론 컴파일타임 타입 검사 능력을 높여준다는 장점도 있다.

---

**태그 달린 클래스를 써야 하는 상황은 거의 없다. 새로운 클래스를 작성하는 데 태그 필드가 등장한다면 태그를 없애고 계층구조로 대체하는 방법을 생각해보자.**