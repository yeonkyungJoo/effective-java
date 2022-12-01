# item8. finalizer와 cleaner 사용을 피하라

#### 자바의 객체 소멸자
- finalizer
- cleaner

#### finalize
- Object 클래스 내 메서드
- 자바는 참조하지 않는 배열이나 객체를 Garbage Collector를 사용해서 힙 영역에서 제거한다.
- Called by the garbage collector on an object when garbage collection determines that there are no more references to the object.
- **vs System.gc()**
  - 명시적으로 가비지 컬렉션이 일어나게 한다.
  - 모든 스레드가 중단되기 때문에 코드단에서 호출 X

#### finalizer
- finalizer은 예측 불가능하고, 위험하며, 대부분 불필요하다. 이를 유용하게 쓸 수 있는 경우는 극히 드물다.
  - 안전망 역할로 자원을 반납하고자 하는 경우
  - 네이티브 리소스를 정리해야 하는 경우
- 자바 9에서는 finalizer가 deprecated 되었고, finalizer보다 덜 위험한 cleaner가 새로 생겼으나 여전히 예측 불가능하며, 느리고, 일반적으로 불필요하다.

##### 단점
1. 언제 실행될 지 알 수 없다. 어떤 객체가 더 이상 필요 없어진 시점에 그 즉시 finalizer나 cleaner가 바로 실행되지 않을 수도 있다. **따라서 타이밍이 중요한 작업은 절대로 finalizer나 cleaner로 하면 안 된다.** 예를 들어, 파일 리소스를 반납하는 작업을 그 안에서 처리한다면, 실제로 그 파일 리소스 처리가 언제 될 지 알 수 없고, 자원 반납이 안 돼 더 이상 새로운 파일을 열 수 없는 상황이 발생할 수도 있다.
2. 특히 finalizer는 인스턴스 반납을 지연시킬 수도 있다. finalizer 스레드는 우선순위가 낮아서 언제 실행될 지 모른다. finalizer 스레드가 어떠한 이유로 계속 대기중이라면, 인스턴스들이 GC가 되지 않고 계속 쌓이다가 결국 OOM이 발생할 수도 있다.
3. finalizer나 cleaner를 아예 실행하지 않을 수도 있다. 그러니 finalizer나 cleaner로 저장소 상태를 변경하는 일을 하지 말라. 데이터베이스 같은 자원의 락을 그것들로 반환하는 작업을 한다면 전체 분산 시스템이 멈춰 버릴 수도 있다.
<code>System.gc</code>나 <code>System.runFinalization</code>에 속지 말라. 그걸 실행해도 finalizer나 cleaner를 바로 실행한다고 보장하진 못한다. 그걸 보장해주겠다고 만든 <code>System.runFinalizersOnExit</code>와 <code>Runtime.runFinalizersOnExit</code>은 수십년간 deprecated 상태다.
4. try-with-resources로 반납하는 것보다 훨씬 느리다.
5. finalizer 공격이라는 심각한 보안 이슈에도 이용될 수 있다.

##### 자원 반납 방법
- 자원 반납이 필요한 클래스 AutoCloseable 인터페이스를 구현하고 try-with-resource를 사용하거나, 클라이언트가 close 메소드를 명시적으로 호출하는게 정석이다. 여기서 추가로 언급하자면, close 메소드는 현재 인스턴스의 상태가 이미 종료된 상태인지 확인하고, 이미 반납이 끝난 상태에서 close가 다시 호출됐다면 IllegalStateException을 던져야 한다.

##### finalizer와 cleaner를 언제 사용하는가?

1. 안전망으로 사용하기 (close를 호출하지 않은 경우)
2. 네이티브 피어(Native Peer) 정리할 때 사용하기
