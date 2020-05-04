## item 28. 배열보다는 리스트를 사용하라

배열은 공변이고 실체화되지만, 제네릭은 불공변이고 타입 정보가 소거된다.

따라서 배열은 런타임에는 안전하지만, 컴파일타임에는 안전하지 않다.

반면에 제네릭은 컴파일타임에 안전하다.

### 배열과 제네릭 타입의 차이 두가지

1) 배열은 함께 변하고 제네릭은 함께 변하지 않는다.

2) 배열은실체화 된다.
   - 배열은 런타임시에도 자신이 담기로 한 원소의 타입을 인지하고 확인하는 반면 제네릭은 타입정보가 런타임시에 하위호환성을 위해 제거된다.
   - 제네릭 타입안에 배열을 넣게 되면 런타임시 타입이 제거되기에 에러가 발생한다. 이러한 이유로 제네릭 배열이 생성되지 않도록 되어있다.
   - E, List<E>, List<String> 같은 타입을 실체화 불가 타입이라 하며 실체화될 수 있는 타입은 List<?>같은 비 한정적 와일드 카드 타입 뿐이다.

```java
public class Chooser {
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        this.choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```
choose() 메서드를 사용하는 곳에서는 반환된 Object를 매번 형변환 해야 한다. 

혹시나 타입이 다른 원소가 들어 있었다면 런타임에 형변환 오류가 날 것이다.

위의 코드를 제네릭을 사용하여 바꿔보자.

```java
public class Chooser<T> {
    private final T[] choiceArray;

    public Chooser(Collection<T> choices) {
        // incompatible types 오류를 해결하기 위해 형변환 사용
        this.choiceArray = (T[]) choices.toArray();
    }

    // choose 메소드는 동일하다.
}
```

위의 코드에서는 Unchecked Cast 경고가 발생한다. 

타입 매개변수 T가 어떤 타입인지 알 수 없으니 형변환이 런타임에도 안전한지 보장할 수가 없다는 메시지이다. 

제네릭은 런타임에는 타입 정보가 소거되므로 무슨 타입인지 알 수 없다. 

Unchecked Cast과 같은 비검사 형변환 경고를 제거하려면 배열 대신 리스트를 사용하면 된다.

```java
// 리스트 기반 Chooser - 타입 안전성 확보 !
class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collection<T> choices) {
        this.choiceList = new ArrayList<>(choices);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

배열과 제네릭을 섞어 쓰다가 컴파일 오류나 경고를 만나면, 가장 먼저 배열을 리스트로 대체하는 방법을 적용해보자 !