# 제네릭(Generic)
- 클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법
- 꺽쇠 괄호 `<>` 안에 타입 이름을 기재하여 사용한다.
- 제네릭은 Collection Class나 메서드에서 사용할 내부 데이터 타입을 파라미터로 주듯이 외부에서 지정할 수 있게 해준다.

## 1. 제네릭 타입 매개변수
- 다이아몬드 연산자 `<>` 안에 식별자 기호를 지정하여 파라미터화 할 수 있다.
- 꺽쇠 괄호 안에 넣는 타입 파라미터는 `<T>` 제네릭을 이용한 클래스나 메서드를 설계할 때 사용한다.
- 타입 파라미터를 사용해서 클래스를 정의하면, 나중에 이를 인스턴스화 할 때 `<>` 안에 지정하고 싶은 타입 명을 할당해서, 해당 타입으로 클래스 타입을 지정해줄 수 있다.
    
    ```java
    class FruitBox<T> {
        List<T> fruits = new ArrayList<>();
    
        public void add(T fruit) {
            fruits.add(fruit);
        }
    }
    
    // 여기서 new 생성자 코드의 제네릭 타입을 명시해도 되고, 생략해도 된다.
    FruitBox<Integer> intBox = new FruitBox<>(); 
    FruitBox<Double> doubleBox = new FruitBox<>(); 
    FruitBox<String> stringBox = new FruitBox<>(); 
    FruitBox<Apple> appleBox = new FruitBox<Apple>();
    ```
    
- 여기서 제네릭 타입 전파가 일어난다.
    - 제네릭 클래스를 선언할 때 `<T>` 타입을 지정하면, 해당 타입 정보가 클래스 내부의 모든 `T` 타입 변수 및 메서드에 전파된다.
    - 이를 통해 객체가 생성될 때 타입이 결정되며, 해당 타입이 클래스 전반에 걸쳐 일관성 있게 유지된다.
    

## 2. 타입 파라미터에 할당 가능한 타입?
- Only Reference 타입만 가능하다.
- `int`나 `double` 같은 자바 원시 타입(Primitive Type) 을 제네릭 타입 파라미터로 넘길 수 없다.
- Wrapper 클래스
    - 원시 타입을 감싸는 `Integer` `Double` `Boolean` 등을 제공하여 Reference 타입으로 쓸 수 있다.
    - Java5 부터 Wrapper Class에 대한 AutoBoxing, UnBoxing 기능이 있어서 제네릭 사용 시 자동 변환이 편하게 된다.
        
        ```java
        List<Integer> list = new ArrayList<>();
        list.add(10); // int -> Integer로 자동 변환 (autoboxing)
        int num = list.get(0); // Integer -> int로 자동 변환 (unboxing)
        ```
        
- 타입 파라미터에는 클래스, 인터페이스 등도 오는데, 이는 클래스 간 상속 관계를 이용할 수 있다는 의미이다.
    
    → 객체 지향 프로그래밍의 다형성 원리를 적용할 수 있다!
    
    ```java
    class Fruit { }
    class Apple extends Fruit { }
    class Banana extends Fruit { }
    
    class FruitBox<T> {
        List<T> fruits = new ArrayList<>();
    
        public void add(T fruit) {
            fruits.add(fruit);
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            FruitBox<Fruit> box = new FruitBox<>();
            
            // 다형성 원리 적용
            box.add(new Fruit());
            box.add(new Apple());
            box.add(new Banana());
        }
    }
    ```
    

### 파라미터 종류

- 복수 타입 파라미터
    - 제네릭을 여러 개 사용하는 상황이 필요할 때, 당연히 복수 타입 파라미터 지정이 가능하다.
    - 제네릭 타입 구분은 `<T, U>` 와 같은 형식으로 괄호 안에서 쉼표로 구분하여 넘겨주면 된다.
- 중첩 타입 파라미터
    - 제네릭 객체를 제네릭 타입 파라미터로 받을 수도 있다.
        
        ```java
        public static void main(String[] args) {
            // LinkedList<String>을 원소로서 저장하는 ArrayList
            ArrayList<LinkedList<String>> list = new ArrayList<LinkedList<String>>();
        
            LinkedList<String> node1 = new LinkedList<>();
            node1.add("aa");
            node1.add("bb");
        
            LinkedList<String> node2 = new LinkedList<>();
            node2.add("11");
            node2.add("22");
        
            list.add(node1);
            list.add(node2);
            System.out.println(list);
        }
        ```
        
- 많이 사용하는 파라미터 네이밍 종류
    - 보통 제네릭 파라미터를 표현할 때 `<T>` 로 표현하는데, 이는 관습이고 공식적인 규약은 아니다.
    - 아래는 관습적으로, 오랜 시간 자주 사용된 네이밍 종류이다.
        - `<T>` 타입
        - `<E>`  요소 (ex. 배열 안에 요소들)
        - `<K>`  키 표현 시 (ex. Map <K, V>)
        - `<V>` 리턴 값이나 매핑된 값
        - `<S, U, V>` 여러개의 타입 선언이 필요할 때 알파벳 순차적으로 많이 사용


## 3. 제네릭 사용 이유 및 이점
- 컴파일 타임에 타입 검사를 통한 예외 방지
    - 제네릭은 Java 1.5 에 추가된 기능
    - 1.5 버전 이전에는 여러 타입을 다룰 때, `Object` 타입을 사용했는데, `Object` 객체를 원하는 타입으로 일일히 타입 변환을 해줘야 했다.
        
        → 해당 과정에서 타입 변환 등의 실수를 하면 런타임 에러가 발생한다.
        
        → Human Error에 대한 위험성이 높다.
        
    - 제네릭을 사용하면 이런 실수를 컴파일 타임에 잡아주기 때문에 타입 문제를 잡기 용이하다.
- 불필요한 캐스팅을 없애 성능 향상
    - 특히, 이전 버전에서 `Object` 객체를 사용할 때, 업캐스팅 & 다운캐스팅이 자꾸 일어났다. (캐스팅 비용 오버헤드)
    - 반면, 제네릭은 미리 타입을 지정 & 제한하기 때문에 형변환을 줄이고 타입 검사에 들어가는 메모리도 줄인다.


## 4. 제네릭 사용 시 주의사항
- 제네릭 타입 객체는 생성이 불가하다. (제네릭 타입 자체로 생성하려고 시도하는 경우)
- static 멤버에 제네릭 타입이 올 수 없다.

   → 논리적인 오류,
    static 멤버는 클래스가 동일하게 공유하는 변수로, 제네릭 객체가 생성되기 전에 이미 자료 타입이 정해져야 있어야 한다.
- 제네릭 클래스를 배열로 선언할 수 없다. (제네릭 타입의 배열 선언은 된다)


## 5. 제네릭 객체
### 제네릭 클래스
- 제네릭 클래스를 사용하면, 클래스 내부에서 사용할 데이터 타입을 인스턴스화할 때 결정할 수 있다.


```java
class Box<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

public class Main {
    public static void main(String[] args) {
        Box<Integer> intBox = new Box<>();
        intBox.setValue(100);
        System.out.println(intBox.getValue()); // 100
    }
}
```
        
### 제네릭 인터페이스
- 인터페이스에도 제네릭을 적용할 수 있는데, 인터페이스를 Impl 한 클래스에서도 오버라이딩한 메서드를 제네릭 타입에 맞춰서 똑같이 구현해줘야 한다.
- 여러 데이터 타입을 다룰 수 있는 범용적인 인터페이스를 만들 수 있다.
  → CRUD 같은 데이터 처리를 하는 인터페이스에서 많이 활용 (ex. Repository 인터페이스 만들 때)   
- 코드 중복을 줄이고 유지보수성을 높일 수 있다.

  
```Java
interface Repository<T> {
    void save(T data);
    T find();
}

class UserRepository implements Repository<String> {
    private String user;

    @Override
    public void save(String data) {
        this.user = data;
    }

    @Override
    public String find() {
        return user;
    }
}
```


### 제네릭 함수형 인터페이스
- 람다 표현식(Java 8~) 의 함수형 인터페이스에서 많이 사용한다.
- 함수형 인터페이스(Functional Interface)는 하나의 추상 메서드만 가지는 인터페이스이다.
- 자바에서 기본으로 제공하는 함수형 인터페이스 목록

#### 주요 제네릭 함수형 인터페이스
 | 함수형 인터페이스 | 메서드 형식 | 설명 |
 | --- | --- | --- |
 | `Function<T, R>` | `R apply(T t)` | 입력 `T`를 받아서 결과 `R`을 반환 |
 | `Consumer<T>` | `void accept(T t)` | 입력 `T`를 받아서 소비 (반환값 없음) |
 | `Supplier<T>` | `T get()` | 매개변수 없이 `T` 타입 값을 반환 |
 | `Predicate<T>` | `boolean test(T t)` | `T`를 받아서 `true/false` 반환 |


아래는 `Predicate<T>` 를 사용하여 서비스 레이어에서 동적 필터링 하는 경우를 보여주는 예시


```java
import java.util.ArrayList;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;

class User {
    String name;
    int age;
    boolean isActive;

    public User(String name, int age, boolean isActive) {
        this.name = name;
        this.age = age;
        this.isActive = isActive;
    }

    @Override
    public String toString() {
        return name + " (" + age + "세, 활성화: " + isActive + ")";
    }
}

public class UserService {
    public static List<User> filterUsers(List<User> users, Predicate<User> condition) {
        return users.stream()
                    .filter(condition)
                    .collect(Collectors.toList());
    }

    public static void main(String[] args) {
        List<User> users = new ArrayList<>();
        users.add(new User("Alice", 25, true));
        users.add(new User("Bob", 30, false));
        users.add(new User("Charlie", 22, true));

        // 1. 활성화된 유저만 필터링
        List<User> activeUsers = filterUsers(users, user -> user.isActive);
        System.out.println("활성 유저: " + activeUsers);

        // 2. 25세 이상인 유저만 필터링
        List<User> adultUsers = filterUsers(users, user -> user.age >= 25);
        System.out.println("성인 유저: " + adultUsers);
    }
}

```
    


### 제네릭 메서드
- 클래스 자체가 아닌 메서드 레벨에서 타입을 제네릭하게 설정할 때 사용한다.
- 메서드가 어떤 타입을 받더라도 유연하게 사용할 수 있고, 재사용성이 높다.
- 제네릭 메서드는 클래스의 타입 매개변수와 독립적으로 선언할 수 있어 `static` 메서드에서도 사용 가능하다.
- 사용 타입이 컴파일 시점에 결정되므로 불필요한 명시적 캐스팅을 없애고, 런타임 오류 가능성을 줄일 수 있다.

  
```java
class FruitBox<T> {
	
    // 클래스의 타입 파라미터를 받아와 반환 타입으로 사용하는 일반 메서드
    public T addBox(T x, T y) {
        // ...
    }
    
    // 독립적으로 타입 할당 운영되는 제네릭 메서드
    public static <T> T addBoxStatic(T x, T y) {
        // ...
    }
}
```


## 6. 제네릭 타입 한정하기
- `extends` 를 사용한 제한
    - **`T extends Number`**
        - `T`는 반드시 `Number`의 하위 클래스여야 한다.
        - `Integer`, `Double`, `Float` 같은 타입만 사용할 수 있고 `String` 등은 불가능
        - `extends` 키워드 뒤에 클래스 외에, 인터페이스도 올 수 있다.
            
            → 특정 인터페이스를 구현한 구현체들만 오도록 만들 수도 있다는 뜻!
            
            ```java
            // T는 반드시 Number 또는 그 하위 클래스여야 함
            class NumberBox<T extends Number> {
                private T value;
            
                public void setValue(T value) {
                    this.value = value;
                }
            
                public T getValue() {
                    return value;
                }
            }
            
            public class Main {
                public static void main(String[] args) {
                    NumberBox<Integer> intBox = new NumberBox<>();
                    intBox.setValue(10);
                    System.out.println(intBox.getValue()); // 10
            
                    NumberBox<Double> doubleBox = new NumberBox<>();
                    doubleBox.setValue(10.5);
                    System.out.println(doubleBox.getValue()); // 10.5
                }
            }
            ```
            
- `super` 키워드를 사용한 제한
    - 와일드카드와 함께, 특정 부모 타입을 제한하는 용도로 쓰인다.
    - **`? super Integer`**
        - `Integer`의 부모 타입(`Number`, `Object` 등)도 허용한다.
