# item3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

### **싱글턴(singleton)**
- 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트

###### 장점
- 메모리 낭비 방지

###### 단점
- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어려워질 수 있다. 인터페이스를 구현한 것이 아니라면 가짜(mock) 구현으로 대체할 수 없기 때문이다.
- 내부 변수의 값을 변경해서 테스트하고 싶은 경**

##### 싱글턴을 만드는 방식
- 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 마련해둔다.

**1. public static 멤버가 final 필드인 방식**

```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() {...}

  public void leaveTheBuilding() {...}
}
```
- public, protected 생성자가 없으므로 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.
- 예외적으로 권한이 있는 클라이언트는 리플렉션 API인 <code>AccessibleObject.setAccessible</code>을 사용해 private 생성자를 호출할 수 있다. 이러한 공격을 방지하고자 한다면, 생성자에서 두 번 객체를 생성하려고 할 때 예외를 던지게 하면 된다.

**2. public static 멤버가 정적 팩터리 메서드인 방식**

```java
public class Elvis {
  private static final Elvis INSTANCE = new Elvis();
  private Elvis() {...}
  public static Elvis getInstance() { return INSTANCE; }

  public void leaveTheBuilding() {...}
}
```
- 항상 같은 객체의 참조를 반환하므로 제2의 인스턴스는 결코 만들어지지 않는다. (리플렉션을 통한 예외는 똑같이 적용된다.)
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
- 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.
- 1, 2번 방식으로 만들어진 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다. 모든 인스턴스 필드에 transient를 선언하고, readResolve 메서드를 제공해야만 역직렬화시에 새로운 인스턴스가 만들어지는 것을 방지할 수 있다. 만약 이렇게 하지 않으면 초기화해둔 인스턴스가 아닌 다른 인스턴스가 반환된다.

```java
private Object readResolve() {
    return INSTANCE;
}
```

**3. 원소가 하나인 열거 타입을 선언**

```java
public enum Elvis {
  INSTANCE;

  public void leaveTheBuilding() {...}
}
```
- 싱글턴을 만드는 가장 좋은 방법
- 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
