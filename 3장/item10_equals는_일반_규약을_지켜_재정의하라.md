# item10. equals는 일반 규약을 지켜 재정의하라

##### equals를 재정의하지 않는 경우

- 각 인스턴스가 본질적으로 고유할 경우 : 값을 표현하는 것이 아니라 동작하는 개체를 표현하는 클래스일 경우 ex) Thread
- 인스턴스의 '논리적 동치성'을 검사할 일이 없을 경우
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는 경우 ex) List
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없을 경우
- 인스턴스 통제 클래스인 경우 : 인스턴스가 2개 이상 생성되지 않기 때문에 논리적 동치성은 곧 객체 식별성을 의미한다. ex) Enum, 싱글톤

##### equals를 재정의해야 하는 경우

- 객체 식별성이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되어 있지 않았을 때
- **이러한 점을 이용해서 Map 키와 Set 원소로 사용할 수 있게 된다.**

##### equals 메서드 일반 규약

###### 반사성(reflexivity)

```java
x.equals(x) == true
```
- 객체는 자기 자신과 같아야 한다.
- 인스턴스가 들어있는 컬렉션에 contains()를 호출해 true가 나오면 된다.

###### 대칭성(symmetry)

```java
x.equals(y) == y.equals(x)
```
- 서로에 대한 동치 여부에 똑같이 답해야 한다.

###### 추이성(transivity)

```java
x.equals(y) == true
y.equals(z) == true
x.equals(z) == true
```

- 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다. 이는 상속의 단점 중 하나가 될 수 있다.

###### 일관성(consistency)

- x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.

###### null-아님

```java
x.equals(null) == false
```
