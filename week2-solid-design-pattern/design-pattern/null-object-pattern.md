# Null Object Pattern
- Null Object 패턴은 null을 직접 사용하는 대신, 특정 동작을 수행하는 "빈 객체"를 반환하여 Null 체크를 없애는 패턴이다.  
- 즉, 객체가 없을 경우에도 예외가 발생하지 않도록 "무해한(default) 동작"을 수행하는 객체를 제공한다.

- **기존 방식:** `null`을 반환하고 `if (obj == null)` 체크 후 처리
- **Null Object 패턴:** `null` 대신 특정 "빈 객체"를 반환하여 불필요한 `null` 체크를 없앰


즉, 함수에서 null을 리턴하는 경우에는 함수를 사용하는 곳에서 null을 처리하는 예외처리가 필요했다.


반면, Null Object Pattern을 사용하면, `null` 객체를 처리하는 로직이 전파되는 문제를 줄일 수 있고, null 체크 중복 코드를 줄일 수 있어서 코드가 단순해진다.


이펙티브 자바에서도 null 반환해야 할 상황에서 null을 그대로 반환하지 말고, 빈 컬렉션이나 배열을 반환할 것을 추천하고 있다.


## 사용 예시


```java
// 이전의 null 체크 코드
Employee e = DB.getEmployee("Bob");
if (e != null && e.isTimeToPay(today)) {
    e.pay();
}

// 아무일도 하지 않는 Employee 구현체 인스턴스를 리턴하게 만들면, null 체크 삭제 가능
Employee e = DB.getEmployee("Bob");
if (e.isTimeToPay(today)) {
    e.pay();
}
```


## 해당 패턴을 사용할 때 고려해야할 점
- 시스템에 null 객체를 도입할 때, 코드가 줄어들거나 적어도 그대로 유지될 때 도입해야 한다.
- null 객체를 도입했더니 코드가 길이가 늘어났다면, 처리 비용이 더 늘어나는 것이므로 널 객체를 사용할 필요가 없다.


---
ref.
- [null-object-pattern-1](https://johngrib.github.io/wiki/pattern/null-object/)
- [null-object-pattern-2](https://deviq.com/design-patterns/null-object-pattern)
