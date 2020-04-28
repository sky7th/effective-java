## 추상 클래스보다는 인터페이스를 우선하라

### 골격 구현 클래스
인터페이스로는 타입을 정의하고, 필요하면 디폴트 메서드 몇 개도 함께 제공한다. 그리고 골격 구현 클래스는 나머지 메서드들까지 구현한다. 이렇게 해두면 단순히 골격을 확장하는 것만으로 이 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다. 바로 템플릿 메서드 패턴이다.

관례상 인터페이스 이름이 Interface라면 그 골격 구현 클래스의 이름은 AbstractInterface로 짓는다.

다음 코드는 완벽히 동작하는 List 구현체를 반환하는 정적 팩터리 메서드로, AbstractList 골격 구현으로 활용했다.

```java 
static List<Integer> intArrayAsList(int[] a){
    Objects.requiredNonNull(a);

    return new AbstractList<Integer>(){
        @Override public Integer get(int i){
            return a[i]
        }

        @Override public Integer set(int i, Integer val){
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }

        @Override public int size(){
            return a.length;
        }
    }
}
```

### 골격 구현 작성
가장 먼저 인터페이스를 잘 살펴 **다른 메서드들의 구현에 사용되는 기반 메서드들을 선정한다. 이 기반 메서드들은 골격 구현에서는 추상 메서드가 될 것이다.**

 그 다음으로 **기반 메서드들을 사용해 직접 구현할 수 있는 메서드를 모두 디폴트 메서드로 제공**한다. (단, equals 와 hashCode 같은 Object메서드는 디폴트 메서드로 제공하면 안된다는 사실을 항상 유념하자) 
 
 만약 인터페이스의 메서드 모두가 기반 메서드와 디폴트 메서드가 된다면 골격 구현 클래스를 별도로 만들 이유는 없다. 
 
 **기반 메서드나 디폴트 메서드로 만들지 못한 메서드가 남아 있다면, 이 인터페이스를 구현하는 골격 구현 클래스를 하나 만들어 남은 메서드들을 작성해 넣는다.**
 
골격 구현 클래스에는 필요하면 public이 아닌 필드와 메서드를 추가 해도 된다.

간단한 예로 **Map.Entry 인터페이스**를 살펴보자. getKey, getValue는 확실히 기반 메서드이며, 선택적으로 setValue도 포함할 수 있다. 이 인터페이스는 equals 와 hashCode의 동작 방식도 정의해놨다. **Object 메서드들은 디폴트 메서드로 제공해서는 안되므로, 해당 메서드들은 모두 골격 구현 클래스에 구현한다.**

```java
public abstract class AbstractMapEntry<K, V> implements Map.Entry<K,V>{

    // 변경 가능한 엔트리는 이 메서드를 반드시 재정의해야 한다.
    @Override public V setValue(V value){
        throw new UnsupportedOperationException();
    }

    // 생략.....
    // equals, hashCdoe, toString 구현
}
```
---

일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합하다. 

복잡한 인터페이스라면 구현하는 수고를 덜어주는 **골격 구현을 함께 제공하는 방법을 꼭 고려해보자.**

**골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공**하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다. 

'가능한 한'이라고 한 이유는, 인터페이스에 걸려 있는 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.