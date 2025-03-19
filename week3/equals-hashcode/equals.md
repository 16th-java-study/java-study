# equals

### 1-1. 정의

equals 메서드는 두 참조 변수의 동등 여부를 확인하는 데 사용된다. Object 클래스에 있는 기본 메서드는 == 연산자처럼 참조 값, 즉 메모리상의 주소를 비교한다. 만약 객체의 필드 값을 기준으로 논리적인 비교를 하고 싶다면 equals 메서드를 오버라이딩해야 한다.

### **1-2. 오버라이딩**

equals는 Object 클래스에서 정의되어 있으므로, 오버라이드 시 반드시 `public boolean equals(Object o)` 형태를 유지해야 한다. 또한 아래와 같은 순서를 권장합니다.

1. **자기 자신과의 비교**
    
    `if (this == o) return true;`
    
2. **null 체크**
    
    `if (o == null) return false;`
    
3. **타입 비교**
    
    `getClass()` 또는 `instanceof`를 사용하여 타입이 같은지 확인한다. 상속 관계에서 동등성 비교를 제한하고 싶다면 `getClass()`를 그렇지 않다면 `instanceof`를 사용한다. `instanceof`를 사용할 경우 상속 구조에 따라 대칭성 문제가 발생하지 않도록 주의해야 한다.
    
4. **필드 비교**
동등 여부 판단에 사용하고자 하는 필드들을 비교한다.

기본적으로 제공되는 클래스들은 이러한 방식으로 equals를 오버라이드하여 실제 값을 비교한다. String 클래스 또한 equals 메서드를 다음과 같이 재정의하고 있다.

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    return (anObject instanceof String aString)
        && (!COMPACT_STRINGS || this.coder == aString.coder)
        && StringLatin1.equals(value, aString.value);
}
```

### 1-3. 일반 규약

equals 메서드를 재정의할 때는 다음 5가지의 규약을 지켜야 한다.

1. **반사성 (Reflexivity)**: 
null이 아닌 모든 참조 값 x에 대해 `x.equals(x) == true`여야 한다.
2. **대칭성 (Symmetry)**: null이 아닌 모든 참조 값 x, y에 대해 `x.equals(y) == true`이면 `y.equals(x) == true`여야 한다.
3. **추이성 (Transitivity)**: null이 아닌 모든 참조 값 x, y, z에 대해 `x.equals(y) == true`이고 `y.equals(z) == true`이면 `x.equals(z) == true`여야 한다.
4. **일관성 (Consistency)**: null이 아닌 모든 참조 값 x, y에 대해 `x.equals(y)`는 항상 동일한 결과를 반환해야 한다.
5. **Not null**: 모든 null이 아닌 참조 값 x에 대해 `x.equals(null) == false`여야 한다.

### 1-4. 추가 고려사항

- **불변 객체와 가변 객체**
    
    equals와 hashCode를 오버라이딩할 때 비교에 사용되는 필드가 객체 생성 후에도 변경될 수 있다면 해시 기반 자료구조 사용 시 문제가 발생할 수 있다. 따라서 가능하면 불변 객체를 사용하거나 가변 객체의 경우 주의 깊게 필드 선택을 해야 한다.
    
- **Collection의 동등성 비교**
    
    List, Set 등 컬렉션의 동등성을 확인하기 위해 단순히 equals()를 사용하면 저장 순서가 결과에 영향을 미칠 수 있다. 예를 들어 List는 같은 데이터를 포함하더라도 순서가 다르면 equals() 결과가 false가 된다. 따라서 순서와 상관없이 데이터의 존재 여부를 기준으로 비교하고 싶다면 List를 정렬한 후 비교하거나 별도의 로직을 사용해야 한다.