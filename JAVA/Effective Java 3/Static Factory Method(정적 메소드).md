### ITEM 1: Static Factory Method(정적 메소드)

#### 정적 메소드

public 생성자를 사용해 객체를 생성하는 방법 외 다음과 같이 **public static factory method** 사용해 해당 클래스의 인스턴스를 만드는 방법이 있다.

```java

// boolean의 기본 타입의 값을 받아 Boolean 객체 참조로 변환
public static Boolean valueOf(boolean b) {

    return b ? Boolean.TRUE : Bollean.FALSE;

}

```

이처럼 생성자 대신 정적 팩토리 메소드를 사용하는 것에 대한 장단점은 다음과 같다.

---

#### 장점

1. 이름을 가질수 있다.

- 즉, 생성자는 오버로딩을 지원하지만 메서드 이름이 중복될수는 없다

```java

public class Book {
    private String title;
    private String author;

    // 생성자1
    public Book(String title, String author){
        this.title = title;
        this.author = author;
    }

    /**
     * 생성자는 하나의 시그니처만 사용하므로, 다음과 같이 생성자 생성이 불가능하다.
     *
     */
    public Book(String title){
        this.title = title;
    }

    public Book(String author){
        this.author = author;
    }
}

```

```java

public class Book {
  String title;
  String author;

  public Book(String title, String author){
    this.title = title;
    this.autor = author;
  }

  public Book(){}

  /*
  * withName, withTitle과 같이 이름을 명시적으로 선언할 수 있으며,
  * 한 클래스에 시그니처가 같은(String) 생성자가 여러개 필요한 경우에도 다음과 같이 생성할 수 있다.
  */

  public static Book withAuthor(String author){
    Book book = new Book();
    book.author = author;
    return book;
  }

  public static Book withTitle(String title){
    Book book = new Book();
    book.title = title;
    return book;
  }
}

```

---

2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

```java

    public final class Boolean implements java.io.Serializable,
                                      Comparable<Boolean>
{
    /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code true}.
     */
    public static final Boolean TRUE = new Boolean(true);

    /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code false}.
     */
    public static final Boolean FALSE = new Boolean(false);
         // ...
}

```

- 불변클래스 : 미리 만들어둔 인스턴스를 재활용하여(캐싱)불필요한 객체 생성을 피할 수있다.
- Boolean.TRUE 는 이에 대한 대표적인 예로, 객체를 생성하지 않는다.
  -Flyweight pattern : 데이터를 공유해 메모리를 절약하는 패턴으로 공통으로 사용되는 객체는 한번만 사용되고, pool에 의해서 관리, 사용된다.

- Instance-Controlled Class : 정적 펙토리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지 통제할 수 있다.

  - Singleton / noninstattiable 로 만들 수 있다

---

3. 리턴 타입의 하위 타입 객체를 반환할 수 있다.

- 반환할 객체의 클래스를 자유롭게 선택할 수 있다.

- 인터페이스 기반 프레임워크 : 인터페이스를 정적 펙토리 메서드의 반환 타입으로 사용

  - 인터페이스를 구현한 모든 클래스를 공개하는 것이 아닌 인터페이스만을 공개 할수 있음

```java

// java7 Collections.emptyList()
public Collections(){
      ///...

    public static final List EMPTY_LIST = new EmptyList<>();

    public static final <T> List<T> emptyList() {
        return (List<T>) EMPTY_LIST;
    }

      //...
}

```

---

4. 유연한 매개변수 처리

   - 정적 팩터리 메서드는 가변 인자(varargs) 또는 빌더 패턴과 유사한 방식을 활용해 다양한 매개변수 구성을 유연하게 처리할 수 있다.

```java

public class CustomList<T> {

    public static <T> CustomList<T> of(T... elements) {
        CustomList<T> list = new CustomList<>();
        for (T element : elements) {
            list.add(element);
        }
        return list;
    }
}


```

- 위 예시처럼 가변 인자를 활용하면 간결한 방식으로 객체를 생성할 수 있다.

5. 리팩토링에 유리

   - 생성자 호출을 모두 정적 팩터리 메서드 호출로 통일하면, 내부 구현의 변경이나 리턴 타입 교체 등의 리팩토링을 할 때 외부 코드에 미치는 영향을 최소화할 수 있다.

```java

public class Product {
    private String name;
    private int price;

    private Product(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public static Product create(String name, int price) {
        return new Product(name, price);
    }
}

// 리팩토링 전
Product product = new Product("item", 1000);

// 리팩토링 후
Product product = Product.create("item", 1000);


```

- 생성자를 사용한 코드에서는 리팩토링 시 영향을 받을 가능성이 크지만, 정적 팩터리 메서드를 사용한 코드는 내부 구현을 변경하더라도 외부에 영향을 주지 않는다.

#### 단점

1. public 혹은 protected 생성자가 없이 정적 펙터리 메서드만 제공하는 클래스는 하위 클래스를 생성할 수 없다.

   - 해당 제약은 상속보다는 Composition 사용을 유도하고, Immutable 타입으로 만드려면 해당 제약을 지켜야 한다는 점에서 장점이 될 수 있다.

```java

public final class ImmutableClass {
    private final String value;

    // 정적 팩터리 메서드로만 객체를 생성 가능
    private ImmutableClass(String value) {
        this.value = value;
    }

    public static ImmutableClass from(String value) {
        return new ImmutableClass(value);
    }

    public String getValue() {
        return value;
    }
}

// 하위 클래스 생성 불가능
class SubImmutableClass extends ImmutableClass {  // 컴파일 에러 발생
}


```

2. 프로그래머가 해당 메서드를 찾기 쉽지 않다.

   - 생성자는 API Docs 상단에 모아두었기 때문에 찾기가 쉬우나, 정적 팩터리 메서드는 다른 메서드와 구분 없이 보여주므로 사용자가 인스턴스화할 방법을 알아서 찾아내야한다.

```java

public class ComplexClass {
    private final int real;
    private final int imaginary;

    private ComplexClass(int real, int imaginary) {
        this.real = real;
        this.imaginary = imaginary;
    }

    // 여러 정적 팩터리 메서드 제공
    public static ComplexClass fromCartesian(int real, int imaginary) {
        return new ComplexClass(real, imaginary);
    }

    public static ComplexClass fromPolar(double modulus, double angle) {
        int real = (int) (modulus * Math.cos(angle));
        int imaginary = (int) (modulus * Math.sin(angle));
        return new ComplexClass(real, imaginary);
    }

    public static ComplexClass zero() {
        return new ComplexClass(0, 0);
    }
}


```

3. 오버로드가 많아질 경우 코드 가독성 저하
   정적 팩터리 메서드는 이름을 가질 수 있는 장점이 있지만, 지나치게 많은 오버로드가 존재할 경우 혼란을 초래할 수 있다.

```java

public static MyClass createFromId(String id) { /* ... */ }
public static MyClass createFromName(String name) { /* ... */ }
public static MyClass createFromBoth(String id, String name) { /* ... */ }


```

- 메서드 이름만으로도 혼란스러울 수 있고, 실제로 어떤 메서드를 호출해야 할지 직관적으로 파악하기 어렵다.

---

#### 주로 사용하는 명명 방식

# Static Factory Method 예시

| 메서드                   | 설명                                                                                           | 예제 코드                                                  |
| ------------------------ | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `from`                   | 매개변수를 하나 받아 해당 타입의 인스턴스를 반환 (형변환 method)                               | `Date d = Date.from(instant);`                             |
| `of`                     | 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드                             | `Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);`     |
| `valueOf`                | `from`과 `of`의 더 자세한 버전                                                                 | `BigInteger.valueOf(Integer.MAX_VALUE);`                   |
| `instance / getInstance` | 매개변수를 받을 경우 매개변수로 명시한 인스턴스를 반환하지만 같은 인스턴스임을 보장하지는 않음 | `StackWalker luke = StackWalker.getInstance(options);`     |
| `create / newInstance`   | `instance` 혹은 `getInstance`와 같지만 매번 새로운 인스턴스를 생성해 반환                      | `Object newArr = Array.newInstance(classObj, arrayLen);`   |
| `getType`                | `getInstance`와 같으나 생성할 클래스가 아닌 다른 클래스의 팩터리 메서드를 정의할 때 사용       | `FileStore fs = Files.getFileStore(path);`                 |
| `newType`                | `newInstance`와 같으나 생성할 클래스가 아닌 다른 클래스의 팩터리 메서드를 정의할 때 사용       | `BufferedReader br = Files.newBufferedReader(path);`       |
| `type`                   | `getType`과 `newType`의 간결한 버전                                                            | `List<Complaint> litany = Collections.list(legacyLitany);` |

---

결론적으로, 정적 팩토리 메서드는 객체 생성의 유연성을 제공하지만, 지나치게 복잡한 오버로드나 메서드의 명확한 구분이 필요하지 않으면 코드 가독성에 문제가 생길 수 있습니다.
