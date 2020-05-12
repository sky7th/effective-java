## item 53. 가변인수는 신중히 사용하라

가변인수 메서드를 호출하면 인수의 개수와 길이가 같은 배열을 만들고 인수들을 만들어진 배열에 저장한 후에 가변인수 메서드에 전달해준다. 

**인수가 1개 이상이어야 할 때**는 아래와 같이 가변인수 앞에 필수 매개변수를 받도록 하자.

```java
static int min(int firstArg, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs) {
        if (arg < min) {
            min = arg;
        }
    }
    return min;
}
```

### 가변인수는 성능에 해가 될 수 있다.
가변인수 메서드가 호출될 때마다 배열을 새로 할당하고 초기화하기 때문이다. 

따라서 아래와 같은 패턴으로 변경할 수도 있다.

    ```java
    public void foo() {}
    public void foo(int arg1) {}
    public void foo(int arg1, arg2) {}
    public void foo(int arg1, arg2, arg3) {}
    public void foo(int arg1, arg2, arg3, int... restArg) {}
    ```