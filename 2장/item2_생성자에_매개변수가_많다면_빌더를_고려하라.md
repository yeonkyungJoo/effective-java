# item2. 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩터리와 생성자는 선택적 매개변수가 많을 때 대응하기 어렵다. 이런 케이스에 **점층적 생성자 패턴, 자바 빈즈 패턴, 빌더 패턴**을 고려해볼 수 있다.

### **점층적 생성자 패턴(telescoping constructor pattern)**

- 필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수를 2개까지 받는 생성자, ..., 선택 매개변수를 전부 받는 생성자까지 늘려가는 방식
- 확장하기 어렵다.
- 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

```java
public class NutritionFacts {
	private final int servingSize;  // 필수
	private final int servings;     // 필수

	private final int calories;     // 선택
	private final int fat;          // 선택
	private final int sodium;       // 선택
	private final int carbohydrate; // 선택

	public NutritionFacts(int servingSize, int servings) {
		this(servingSize, servings, 0);
	}

	public NutritionFacts(int servingSize, int servings, int calories) {
		this(servingSize, servings, calories, 0);
	}

	public NutritionFacts(int servingSize, int servings, int calories, int fat) {
		this(servingSize, servings, calories, fat, 0);
	}

	public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
		this(servingSize, servings, calories, fat, 0);
	}

	public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
		this.servingSize = servingSize;
		this.servings = servings;
		this.calories = calories;
		this.fat = fat;
		this.sodium = sodium;
		this.carbohydrate = carbohydrate;
	}

	// 지방(fat)에 0을 작성한 것 처럼 설정하길 원치 않는 매개변수까지 값을 지정해줘야한다.
	// NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
}
```

### **자바 빈즈 패턴(JavaBeans pattern)**

- 매개변수가 없는 생성자로 객체를 만든 후, 세터(setter) 메서드를 호출해 원하는 매개변수의 값을 설정하는 방식
- 인스턴스를 만들기 쉽고 가독성이 좋아진다.
- 객체를 하나 만들기 위해 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓인다. (생성자를 통한 유효성 검사라는 장치가 사라짐)
- **불변으로 만들 수 없으며 스레드 안정성을 위한 추가 작업이 필요하다.**

```java
public class NutritionFacts {
	private int servingSize = -1;  // 필수
	private int servings = -1;     // 필수
	private int calories = 0;
	private int fat = 0;
	private int sodium = 0;
	private int carbohydrate = 0;

	public NutritionFacts() {}

	public void setServingSize(int val) { servingSize = val; }
	public void setServings(int val) { servings = val; }
	public void setCalories(int val) { calories = val; }
	public void setFat(int val) { fat = val; }
	public void setSodium(int val) { sodium = val; }
	public void setCarbohydrate(int val) {carbohydrate = val; }
}


// 점층적 생성자 패턴에 비해 확장하기 쉽고, 인스턴스를 만들기 쉽고, 가독성이 좋아진다.
NutritinFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```

### **빌더 패턴**

- 필수 매개변수와 생성자(혹은 정적 팩터리)를 통해 객체 생성을 위한 빌더 객체를 얻고, 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수를 설정, 마지막으로 build 메서드를 호출해 (보통은 불변인) 타겟 객체를 얻는 방식
- 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 메서드 연쇄(method chaining)가 가능하다.
- **점층적 생성자 패턴의 안정성(유효성 검사)과 자바 빈즈 패턴의 가독성이라는 장점을 가진다.**

```java
public class NutritionFacts {
	private final int servingSize;
	private final int servings;

	private final int calories;
	private final int fat;
	private final int sodium;
	private final int carbohydrate;

// 가장 먼저 필요한 것은, 빌더 클래스를 따로 만드는 것이다.
// 이 클래스는 빌드하려는 객체와 강하게 연관되어 있으므로 내부 정적 클래스로 정의한다.
	public static class Builder {
		private final int servingSize;  // 필수
		private final int servings;     // 필수

		private int calories = 0;
		private int fat = 0;
		private int sodium = 0;
		private int carbohydrate = 0;

// 1. 빌더 객체를 사용하려면 가장 먼저 필수 파라미터를 입력하도록 한다.
		public Builder(int servingSize, int servings) {
			this,servingSize = serginsSize;
			this.servings = servings;
		}

// 2. 옵셔널 파라미터는 선택적으로 호출되도록 한다.
		public Builder fat(int val) {
			fat = val;
			return this;
		}

		public Builder sodium(int val) {
			sodium = val;
			return this;
		}

		public Builder carbohydrate(int val) {
			carbohydrate = val;
			return this;
		}

// 3. 옵셔널 세팅 작업이 완료되면 완성 메서드를 호출한다.
		public NutritionFacts build() {
			return new NutritionFacts(this);
		}
	}

// 4. 객체는 반드시 해당 객체의 Builder 객체로만 생성할 수 있도록 한다.
	private NutirionFacts(Builder builder) {
		servingSize = builder.servingSize;
		servings = builder.servings;
		calories = builder.calories;
		fat = builder.fat;
		sodium = builder.fat;
		carbohydrate = builder.carbohydrate;
	}
}

NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
	.calories(100)
	.sodium(35)
	.carbohydrate(27)
	.build();
```

- **빌더 패턴으로 생성하는 객체는 불변인가?**
    - 위에서 정의한 클래스를 살펴보면, 필드를 모두 final로 정의했다.
    - 클래스의 생성자 또한 private이며 setter 메서드는 존재하지 않는다.
- 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기 좋다.

```java
NyPizza pizza = new NyPizza.Builder(SMALL)
	.addTopping(SAUSAGE)
	.addTopping(ONION)
	.build();

Calzone pizza = new Calzone.Builder()
	.addTopping(HAM)
	.sauceInside()
	.build();
```

```java
public abstract class Pizza {
  public enum Topping {
    HAM, MUSHROOM, ONION, PEPPER, SAUSAGE
  }
  final Set<Topping> toppings;

  abstract static class Builder<T extends Builder<T>> {
    EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
    public T addTopping(Topping topping) {
      toppings.add(Objects.requireNonNull(topping));
      return self();
    }

    abstract Pizza build();

    protected abstract T self();
  }

  Pizza(Builder<?> builder) {
    toppings = builder.toppings.clone();
  }
}

public class NyPizza extends Pizza {
	public enum Size { SMALL, MEDIUM, LARGE }
	private final Size size;

	public static class Builder extends Pizza.Builder<Builder> {
		private final Size size;

		public Builder(Size size) {
			this.size = Objects.requireNonNull(size);
		}

		// 공변 반환 타이핑(covariant return typing)을 사용하면, 빌더를 사용하는 클라이언트는 캐스팅이 필요없다.
		@Override
		public NyPizza build() {
			return new NyPizza(this);
		}

		// 부모 타입에서 필요한 서브 타입의 참조
		@Override
		protected Builder self() {
			return this;		
		}

		public NyPizza(Builder builder) {
			super(builder);
			size = builder.size;
		}
	}
}

public class Calzone extends Pizza {
	private final boolean sauceInside;

	public static class Builder extends Pizza.Builder<Builder> {
		private boolean sauceInsice = false;

		public Builder sauceInside() {
			sauceInsdie = true;
			return this;
		}

		@Override
		public Calzone build() {
			return new Calzone(this);
		}

		@Override
		protected Builder self() {
			return this;
		}

		private Calzone(Builder builder) {
			super(builder);
			sauceInside = builder.sauceInside;
		}
	}
}
```
- 생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.
