# item6. 불필요한 객체 생성을 피하라

- 기능적으로 동일한 객체는 새로 만들기 보다 객체 하나를 재사용하는 것이 더 적절하다.
- 불변 객체는 항상 재사용할 수 있다.
- **그렇다고 객체를 만드는 것은 비싸며 가급적이면 피해야 한다는 오해를 해서는 안 된다. 특히 방어적인 복사(Depensive Copying)를 해야 하는 경우에도 객체를 재사용하면 심각한 버그와 보안성에 문제가 생긴다.**

#### 문자열 객체 생성
- String을 new로 생성하면 항상 새로운 인스턴스를 생성한다. String 인스턴스는 다음과 같이 생성하는 것이 올바르다.
  ```java
  String s = "string";
  ```
- JVM에 동일한 문자열 리터럴이 존재한다면 그 리터럴을 재사용한다.

#### static 팩토리 메서드
- 자바 9에서 deprecated된 <code>Boolean(String)</code> 대신 <code>Boolean.valueOf(String)</code> 같은 static 팩토리 메서드를 사용할 수 있다. 생성자는 반드시 새로운 객체를 만들어야 하지만 팩토리 메서드는 그렇지 않다.

#### 무거운 객체
- 생성 시 메모리나 시간이 많이 드는 객체, 즉 '비싼 객체'를 반복적으로 만들어야 한다면 캐시해두고 재사용할 수 있는지 고려하는 것이 좋다.
  ```java
  static boolean isRomanNumeral(String s) {
      return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
  }
  ```

  ```java
  public class RomanNumber {

      // Pattern 객체를 만들어 재사용하는 것이 좋다.
      private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

      static boolean isRomanNumeral(String s) {
          return ROMAN.matcher(s).matches();
      }

  }
  ```
  - 위 코드도 문제가 있는데 isRomanNumeral()이 호출되지 않는다면 ROMAN은 필요없이 만든 셈이 된다. 게으른 초기화(지연 초기화, lazily initializing)를 사용해서 최적화할 수 있지만 추천하지는 않는다. 이는 측정 가능한 성능 개선 없이 구현을 복잡하게 만든다.

#### 어댑터
- 어댑터는 인터페이스를 통해서 뒤에 있는 객체로 연결해주는 객체라 여러 개 만들 필요가 없다.
- Map 인터페이스가 제공하는 keySet은 뒤에 있는 Set 인터페이스의 뷰를 제공한다. keySet을 호출할 때마다 새로운 객체가 나올 것 같지만 사실은 같은 객체를 리턴하기 때문에, 리턴 받은 Set타입의 객체를 변경하면, 결국에는 그 뒤에 있는 Map 객체를 변경하게 된다.

#### 오토박싱
- 불필요한 객체를 생성하는 또 다른 방법
- 불필요한 오토박싱을 피하려면 박스 타입보다는 프리미티브 타입을 사용해야 한다.
