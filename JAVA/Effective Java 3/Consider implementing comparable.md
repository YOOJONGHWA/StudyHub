## ITEM 14: Consider implementing comparable

#### Comparable 인터페이스와 compareTo 사용하기

Comparable은 순서를 정의할 수 있는 제네릭 인터페이스입니다. 이를 구현하면 자연 순서를 가지는 객체를 만들 수 있습니다. 자연 순서가 있는 객체는 정렬, 검색 등에 활용할 수 있습니다.

---

1. Comparable 기본 사용법

   1. compareTo 메서드를 구현합니다.

   2. 비교 결과:

      - 음수: 현재 객체가 비교 대상보다 작음.

      - 0: 두 객체가 같음.

      - 양수: 현재 객체가 비교 대상보다 큼.

```java

public class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age); // 나이 순 정렬
    }
}

```

---

2. 배열 또는 컬렉션 정렬하기
   Arrays.sort와 Collections.sort를 사용하면 Comparable 구현을 자동으로 활용합니다.

```java

public static void main(String[] args) {
    Person[] people = {
        new Person("Alice", 30),
        new Person("Bob", 25),
        new Person("Charlie", 35)
    };

    Arrays.sort(people);

    for (Person person : people) {
        System.out.println(person.name + ": " + person.age);
    }
}

출력:

Bob: 25
Alice: 30
Charlie: 35

```

---

3. Comparator를 활용한 유연한 비교
   Comparator를 사용하면 compareTo 외에도 여러 정렬 기준을 정의할 수 있습니다.

- 기본 Comparator:

```java

Comparator<Person> byName = Comparator.comparing(person -> person.name);

```

- 중첩 정렬:

```java

Comparator<Person> byAgeThenName = Comparator
    .comparingInt((Person p) -> p.age)
    .thenComparing(p -> p.name);

```

- 사용:

```java

List<Person> personList = Arrays.asList(
    new Person("Alice", 30),
    new Person("Bob", 25),
    new Person("Charlie", 30)
);

personList.sort(byAgeThenName);

for (Person p : personList) {
    System.out.println(p.name + ": " + p.age);
}

출력:

Bob: 25
Alice: 30
Charlie: 30

```

---

4. 구현 팁

- <, > 대신 Integer.compare, Double.compare 사용:

```java

@Override
public int compareTo(Person other) {
    return Integer.compare(this.age, other.age);
}

```

- 핵심 필드 우선 비교:

```java

@Override
public int compareTo(Person other) {
    int result = Integer.compare(this.age, other.age);
    if (result == 0) {
        result = this.name.compareTo(other.name);
    }
    return result;
}

```

- Comparator를 활용한 간결한 구현:

```java

private static final Comparator<Person> COMPARATOR =
    Comparator.comparingInt((Person p) -> p.age)
              .thenComparing(p -> p.name);

@Override
public int compareTo(Person other) {
    return COMPARATOR.compare(this, other);
}

```

---

5. 규약 준수

- compareTo와 equals의 일관성:

  - 두 객체가 같으면 compareTo == 0이어야 합니다.

- 이를 어기면 TreeSet, TreeMap 등에서 예기치 않은 동작이 발생할 수 있습니다.

---

#### 정리

- Comparable: 자연 순서를 정의.

- Comparator: 유연한 비교 기준 제공.

- compareTo 작성법:

  - 필드 순서대로 비교.

  - <, > 대신 Integer.compare 사용.

- 도구 활용:

  - Comparator.comparing으로 간결하게 작성.
