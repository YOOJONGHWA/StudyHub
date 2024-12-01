## ITEM 8: Avoid finalizer and cleaner

자바는 객체가 더 이상 사용되지 않을 때 자원을 정리하기 위해 finalizer와 cleaner라는 소멸자를 제공하지만, 여러 문제로 인해 사용을 지양해야 합니다. 대신, 더 안전하고 효과적인 대안을 사용해야 합니다.

---

#### 왜 finalizer와 cleaner를 피해야 할까?

1. 예측 불가능성

- 즉시 실행 보장 없음: GC가 언제 수행될지 알 수 없고, 따라서 finalizer/cleaner가 언제 실행될지도 알 수 없습니다.

- 수행 여부 보장 없음: 프로그램이 종료되더라도 객체의 정리 작업이 수행되지 않을 수 있습니다.

2. 성능 저하

- finalizer는 GC 성능을 심각하게 저하시킵니다.

- cleaner는 finalizer보다 덜하지만 여전히 성능 문제를 유발합니다.

3. 보안 문제

- 객체 생성 중 예외가 발생하면 finalizer 공격에 노출될 수 있습니다.

- 악의적인 하위 클래스가 finalizer를 통해 시스템 자원을 제어할 수 있습니다.

---

#### 대안: AutoCloseable과 try-with-resources

AutoCloseable 인터페이스를 구현하고 명시적으로 자원을 닫는 close() 메서드를 제공하면, 위의 문제를 피할 수 있습니다.

---

#### 구현 방법

1. 클래스가 AutoCloseable을 구현하도록 선언합니다.

2. 자원을 닫는 로직을 close() 메서드에 작성합니다.

3. 객체가 닫힌 상태를 확인하는 플래그를 추가하고, 닫힌 상태에서 메서드 호출 시 예외를 던지도록 합니다.

4. try-with-resources를 사용하면 자동으로 close() 메서드가 호출됩니다.

```java

import java.io.Closeable;

class Resource implements AutoCloseable {
    private boolean closed = false; // 자원 상태를 추적하는 플래그

    public void use() {
        if (closed) {
            throw new IllegalStateException("자원이 이미 닫힌 상태입니다!");
        }
        System.out.println("자원을 사용합니다.");
    }

    @Override
    public void close() {
        if (!closed) {
            closed = true; // 닫힘 상태로 변경
            System.out.println("자원을 닫습니다.");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        try (Resource resource = new Resource()) { // try-with-resources 사용
            resource.use(); // 자원을 사용
        } // 블록을 빠져나오며 close() 자동 호출
    }
}

```

---

#### 동작 방식

1. try-with-resources 블록 안에서 자원을 사용합니다.

2. 블록이 종료되면 자원의 close() 메서드가 자동 호출되어 자원이 정리됩니다.

3. closed 플래그를 통해 자원이 이미 닫힌 상태에서의 잘못된 호출을 방지합니다.

---

#### 장점

- 예측 가능: try-with-resources는 자원이 명확히 정리되는 시점을 보장합니다.

- 성능 향상: 불필요한 GC 부하를 줄일 수 있습니다.

- 안전성: 자원이 닫힌 상태에서의 잘못된 사용을 방지할 수 있습니다.

- 결론적으로, finalizer와 cleaner 대신 AutoCloseable과 try-with-resources를 사용하여 안전하고 효율적으로 자원을 관리하자.
