## 제네릭과 와일드카드 개념

1. 제네릭 매개변수화 타입

   ```java
   List<String>: List는 제네릭 타입이고, String은 실제 타입 매개변수.
   ```

2. 상한 경계 와일드카드 (<? extends E>)

생산자(Producer)일 때 사용

- 문제 상황:

  ```java

  - Stack<Number>에 Iterable<Integer>를 넣으려 하면 오류 발생.
  - Iterable<Integer>는 Iterable<Number>의 하위 타입이 아니기 때문.

  ```

해결 코드:

```java

public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}

```

사용 예:

```java

Stack<Number> stack = new Stack<>();
Iterable<Integer> integers = List.of(1, 2, 3);
stack.pushAll(integers); // 정상 동작

```

3. 하한 경계 와일드카드 (<? super E>)
   소비자(Consumer)일 때 사용

문제 상황:

```java
Stack<Number>의 값을 Collection<Object>에 옮기려 하면 오류 발생.
Collection<Object>는 Collection<Number>와 아무 관계가 없기 때문.

```

해결 코드:

```java

public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}

```

사용 예:

```java

Stack<Number> stack = new Stack<>();
Collection<Object> objects = new ArrayList<>();
stack.popAll(objects); // 정상 동작

```

4. PECS 원칙

- Producer-Extends, Consumer-Super

  - 생산자 → <? extends T>

  - 소비자 → <? super T>

5. 와일드카드 적용 예제: max 메서드

문제 상황:

```java
ScheduledFuture<?>와 같은 타입을 처리하지 못함.
```

수정 전 코드:

```java

public static <E extends Comparable<E>> E max(List<E> c) { ... }

```

수정 후 코드:

```java
public static <E extends Comparable<? super E>> E max(List<? extends E> c) {
    if (c.isEmpty()) {
        throw new IllegalArgumentException("collection is empty");
    }
    E result = null;
    for (E e : c) {
        if (result == null || e.compareTo(result) > 0) {
            result = Objects.requireNonNull(e);
        }
    }
    return result;
}

```

변경 이유:

- 입력 리스트가 E를 생성하므로 List<? extends E> 사용.

- Comparable은 소비자 역할이므로 Comparable<? super E> 사용.

6. Helper 메서드로 와일드카드 캡처 해결

문제 상황:

```java
List<?>에서 특정 타입 추론이 불가능하여 swap 메서드 호출 시 컴파일 오류 발생.

```

해결 코드:

```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j);
}

// Helper 메서드
private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}

```

사용 예:

```java

List<String> list = new ArrayList<>(List.of("A", "B", "C"));
swap(list, 0, 2);
System.out.println(list); // 출력: [C, B, A]

```

설명:

- swapHelper 메서드 덕분에 List<?>의 타입이 E로 추론됨.

- 컴파일러가 타입 안정성을 보장.

#### 핵심 요약

- 상한 경계 와일드카드 (<? extends T>): 데이터를 읽을 때 사용.

- 하한 경계 와일드카드 (<? super T>): 데이터를 쓸 때 사용.

- Helper 메서드: 와일드카드 타입을 추론하기 위해 사용.

- PECS 원칙: 생산자는 extends, 소비자는 super.
