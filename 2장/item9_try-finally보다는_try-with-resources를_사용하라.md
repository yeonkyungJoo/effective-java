# item9. try-finally보다는 try-with-resources를 사용하라

- InputStream, OutputStream, java.sql.Connection 등의 리소스는 정리가 필요하다.

- 아래 코드 실행 결과, SecondException이 출력되고 FirstException은 출력되지 않는다. 이렇게 되면 문제를 디버깅하기 힘들어진다.

  ```java
  public class FirstError extends RuntimeException {
  }

  public class SecondException extends RuntimeException {
  }

  public class MyResource implements AutoCloseable {

      public void doSomething() throws FirstError {
          System.out.println("doing something");
          throw new FirstError();
      }

      @Override
      public void close() throws SecondException {
          System.out.println("clean my resource");
          throw new SecondException();
      }
  }
  ```

  ```java
  MyResource myResource = null;
  try {
      myResource = new MyResource();
      myResource.doSomething();
  } finally {
      if (myResource != null) {
          myResource.close();
      }
  }
  ```

- Java7에 추가된 try-with-resources를 사용하면 코드 가독성도 좋고, 위 결과처럼 처음 발생한 예외가 뒤에 발생한 에러에 덮히지 않는다. 뒤에 발생한 에러는 처음 발생한 에러 뒤에다가 쌓아두고(suppressed), 처음 발생한 에러를 중요하게 여긴다. 그리고 Throwable의 getSuppressed 메소드를 사용해서 뒤에 쌓여있는 에러를 코딩으로 사용할 수도 있다.
