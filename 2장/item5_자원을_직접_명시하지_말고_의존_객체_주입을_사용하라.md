# item5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

```java
public class SpellChecker {
    private static final Lexicon dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions (String typo) { ... }
}

public class SpellChecker {
    private final Lexicon dictionary = ...;

    private SpellChecker() {}
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public boolean isValid(String word) { ... }
    public List<String> suggestions (String typo) { ... }
}
```
- dictionary가 final로 선언되어 있어 변경이 불가하다. → 유연하지 않고 테스트가 어렵다.
- final을 제거하고 다른 사전으로 교체하는 메서드를 추가할 수 있지만 이는 멀티스레드 환경에서는 사용이 불가하다. **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**
- 인스턴스를 생성할 때 생성자에 필요한 자원을 넣어주는 방식을 사용해야 한다.

#### 의존 객체 주입 패턴
```java
public class SpellChecker {
    private final Lexicon dictionary = ...;

    private SpellChecker() {}
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public boolean isValid(String word) { ... }
    public List<String> suggestions (String typo) { ... }
}
```
- 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.
- 불변을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 객체들을 안심하고 공유할 수 있다.
- 의존 객체 주입은 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있다.

#### 팩터리 메서드 패턴(Factory Method Pattern)
- 생성자에 자원 팩터리를 넘겨주는 방식
- **팩터리란 호출할 때마다 특정 타입의 인스턴스는 반복해서 만들어주는 객체를 말한다.**
- Supplier 인터페이스 : Supplier를 입력으로 받는 메서드는 일반적으로 한정적 와일드카드 타입을 사용해 팩터리의 타입 매개변수를 제한한다. 이 방식을 사용해 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다.
```java
Mosaic create(Supplier<? extends Tile> tileFactory) {..}
```
