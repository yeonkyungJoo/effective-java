# item4. 인스턴스화를 막으려거든 private 생성자를 사용하라

- 정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 사용하려고 설계한 것이 아니다.
- 하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어준다.
- 추상 클래스로 만드는 것도 인스턴스화를 막을 수 없다. 하위 클래스를 만들어 인스턴스화하면 그만이다.
- **private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.**

```java
public class UtilityClass {
  private UtilityClass() {
    throw new AssertionError();
  }
}
```
- 명시적 생성자가 private이니 클래스 바깥에서는 접근할 수 없다.
- AssertionError를 던질 필요는 없지만, 클래스 안에서 실수로라도 생성자를 호출하지 않도록 해준다.
- **상속을 불가능하게 하는 효과도 있다. 모든 생성자는 명시적이든 묵시적이든 상위 클래스의 생성자를 호출하게 되는데, 이를 private으로 선언했으니 하위 클래스가 상위 클래스의 생성자에 접근할 길이 막혀버린다.**


##### Example. java.lang.Math
```java
public final class Math {

    /**
     * Don't let anyone instantiate this class.
     */
    private Math() {}

    public static final double E = 2.7182818284590452354;
    public static final double PI = 3.14159265358979323846;

    // ...

    public static int max(int a, int b) {
        return (a >= b) ? a : b;
    }
}
```
