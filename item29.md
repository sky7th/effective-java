## item 29. 이왕이면 제네릭 타입으로 만들라

```java
// Object 기반 스택 - 제네릭이 절실한 강력 후보!
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object e){
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop(){
        if(size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }
    
    public boolean isEmpty(){
        return size == 0;
    }
    
    private void ensureCapacity(){
        if(elements.length == size){
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

**E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.**

이에 대한 적절한 해결책은 두 가지다.
1. 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법
2. elements 필드의 타입을 E[]에서 Object[]로 바꾸는 방법

첫 번째 방식에서는 형변환을 배열 생성 시 단 한 번만 해주면 되지만, 두 번쨰 방식에서는 배열에서 원소를 읽을 때마다 해줘야 한다.

현업에서는 첫 번째 방식을 더 선호하며 자주 사용한다.

하지만 배열의 런타임 타입이 컴파일타임 타입과 달라 힙 오염(item 32)을 일으킨다.

아래의 코드는 첫 번째 방법을 사용한 코드이다.


```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
 
    
    // 배열 elements 는 push(E)로 넘어온 E 인스턴스만 담는다.
    // 따라서 타입 안전성을 보장하지만,
    // 이 배열의 런타임 타입은 E[]가 아닌 Object[]다!
    @SuppressWarnings("unchecked")
    public Stack(){
        elements = (E[]) new Object [DEFAULT_INITIAL_CAPACITY]}
    }

 
    public void push(E e){
        ensureCapacity();
        elements[size++] = e;
    }
 
    public E pop(){
        if(size == 0){
            throw new EmptyStackException();
        }
 
        E result = elements[--size];
        elements[size] = null;
        return result;
    }
 
    public boolean isEmpty(){
        return size == 0;
    }
 
    private void ensureCapacity(){
        if(elements.length == size){
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

**제네릭 타입 안에서 리스트를 사용하는 게 항상 가능하지도, 꼭 더 좋은 것도 아니다.**

자바가 리스트를 기본 타입으로 제공하지 않으므로 ArrayList 같은 제네릭 타입도 결국은 기본 타입인 배열을 사용해 구현해야 한다.

또한 HashMap 같은 제네릭 타입은 성능을 높일 목적으로 배열을 사용하기도 한다.

---

클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편하다. 

그러니 **새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라.**

기존 클라이언트에는 아무 영향을 주지 않으면서, 새로운 사용자를 훨씬 편하게 해주는 길이다.(item 26)