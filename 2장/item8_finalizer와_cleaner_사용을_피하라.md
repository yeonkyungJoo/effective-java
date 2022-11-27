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
