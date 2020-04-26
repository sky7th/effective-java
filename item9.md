## 아이템 9. try-finally 보다는 try-with-resources를 사용하라.
​
자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. 자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어지기도 한다.
​
-   try-finally 문이 중첩될 경우 두 번째 예외가 첫 번째 예외를 집어삼켜 버려서 디버깅이 어렵다.
-   try-finally에서처럼 try-with-resources에서도 catch 절을 쓸 수 있어서 try 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있다.
-   꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자.