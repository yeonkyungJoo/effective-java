# item12. toString을 항상 재정의하라

#### toString()
- Object 클래스의 메서드
- println, printf, 문자열 연결 연산자(+), assert 구문 등에서 자동으로 사용된다.

```java
// 클래스 이름@16진수로 표시한 해시코드
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

- 해당 객체의 내용을 명확하게 전달할 수 있도록 toString을 재정의하는 것이 좋다. 디버깅이 쉬워진다.
- 모든 구체 클래스에서 toString을 재정의하자. 상위 클래스에서 이미 알맞게 재정의한 경우는 예외다.

```java
// AbstractMap<K,V>의 toString()
public String toString() {
    Iterator<Entry<K,V>> i = entrySet().iterator();
    if (! i.hasNext())
        return "{}";

    StringBuilder sb = new StringBuilder();
    sb.append('{');
    for (;;) {
        Entry<K,V> e = i.next();
        K key = e.getKey();
        V value = e.getValue();
        sb.append(key   == this ? "(this Map)" : key);
        sb.append('=');
        sb.append(value == this ? "(this Map)" : value);
        if (! i.hasNext())
            return sb.append('}').toString();
        sb.append(',').append(' ');
    }
}

// AbstractCollection의 toString()
public String toString() {
    Iterator<E> it = iterator();
    if (! it.hasNext())
        return "[]";

    StringBuilder sb = new StringBuilder();
    sb.append('[');
    for (;;) {
        E e = it.next();
        sb.append(e == this ? "(this Collection)" : e);
        if (! it.hasNext())
            return sb.append(']').toString();
        sb.append(',').append(' ');
    }
}
```

- toString() 재정의 시 순환 참조가 있다면 StackOverflowError 발생
