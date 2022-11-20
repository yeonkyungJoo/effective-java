# item1. 생성자 대신 정적 팩터리 메서드를 고려하라

- 클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 public 생성자다.
- 이 외에 클래스는 생성자와 별도로 정적 팩터리 메서드(static factory method)를 제공할 수 있다. 그 클래스의 인스턴스를 반환하는 단순한 정적 메서드이다.

```java
public class Person {
  private String name;
  private int age;

  public Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  public static Person createPerson(String name, int age) {
    //return new Person();
    return new Person(name, age);
  }
}
```

##### 장점

1. **이름을 가질 수 있다.**

  - 반환될 객체의 특성을 쉽게 묘사할 수 있다.
  - 하나의 시그니처로 여러가지 객체를 생성할 수 있다. (매개변수의 타입과 개수가 같은 생성자를 여러 개 만들 수 없다.)


2. **호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.**

  - 불변 클래스
  - 불필요한 객체 생성을 피할 수 있다.
  - 플라이웨이트 패턴


3. **반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.**

  - 리턴 타입은 인터페이스로 지정하고, 실제로는 인터페이스의 구현체를 리턴함으로서 구현체의 API는 노출시키지 않고 객체를 생성할 수 있다.
  - 이는 주로 동반 클래스에서 볼 수 있다.

  ```java
  public static <T> List<T> unmodifiableList(List<? extends T> list) {
        return (list instanceof RandomAccess ?
                new UnmodifiableRandomAccessList<>(list) :
                new UnmodifiableList<>(list));
}
  ```


4. **입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.**

  ```java
  public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
          Enum<?>[] universe = getUniverse(elementType);
          if (universe == null)
              throw new ClassCastException(elementType + " not an enum");

          if (universe.length <= 64)
              return new RegularEnumSet<>(elementType, universe);
          else
              return new JumboEnumSet<>(elementType, universe);
      }
  ```
  - 메서드 내부에서 universe.length 따라 리턴 타입을 다르게 반환 한다.
  - 클라이언트는 내부 구현을 알 필요 없이 원하는 객체를 리턴받을 수 있고, 구현이 변경되어도 클라이언트에게 영향을 끼치지 않는다.


5. **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.**


##### 단점
1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.


2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
