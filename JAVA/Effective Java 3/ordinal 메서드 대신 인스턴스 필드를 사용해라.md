## ITEM 35: ordinal 메서드 대신 인스턴스 필드를 사용하라

- 열거 타입(enum)의 상수는 각 상수가 해당 열거 타입에서 몇 번째 위치인지를 나타내는 정수 값을 반환하는 ordinal 메서드를 제공합니다.

- 하지만, 상수와 관련된 값을 얻기 위해 ordinal 메서드를 사용하는 것은 매우 위험하며, 대체로 권장되지 않습니다.

- 대신, 열거 타입 상수에 연결된 값을 별도의 인스턴스 필드에 저장하고 사용하는 것이 안전하고 유지보수에도 유리합니다.

---

#### 잘못된 예: ordinal 사용

```java

public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET, SEXTET, SEPTET, OCTET, NONET, DECTET;

    // ordinal을 잘못 사용한 경우
    public int numberOfMusicians() {
        return ordinal() + 1;
    }
}

```

#### 문제점

- 상수 순서 변경 문제

  - 상수 선언 순서를 바꾸는 순간, numberOfMusicians 메서드가 반환하는 값이 달라져 의도하지 않은 동작이 발생합니다.

- 값의 중복 추가 불가

  - 예를 들어, 8명이 연주하는 "OCTET"과 "DOUBLE_QUARTET"이 같은 의미라도 ordinal 값은 중복될 수 없어 추가가 불가능합니다.

- 중간 값의 비어 있음 문제

  - 특정 값을 추가하려면, 불필요한 더미 상수를 추가해야 하는 경우가 생깁니다.

  - 예를 들어, 12명 연주를 나타내는 "TRIPLE_QUARTET"을 추가하려면, 11명을 나타내는 상수를 추가해야 하는 비효율성이 발생합니다.

#### 권장 예: 인스턴스 필드 사용

```java

public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5), SEXTET(6), SEPTET(7), OCTET(8),
    DOUBLE_QUARTET(8), NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;

    Ensemble(int size) {
        this.numberOfMusicians = size;
    }

    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}

```

#### 장점

- 순서 변경에 안전

  - 상수 선언 순서를 바꾸더라도 numberOfMusicians 값은 변하지 않습니다.

- 값 중복 가능

  - 동일한 값을 가진 상수를 자유롭게 추가할 수 있습니다. 예: OCTET과 DOUBLE_QUARTET.

- 값 비워두기 가능

  - 필요한 값만 추가하고 중간 값을 비워둘 수 있어 더미 상수를 추가하지 않아도 됩니다.

##### ordinal 메서드의 사용 제한

- ordinal 메서드는 열거 타입 기반의 범용 자료구조(예: EnumSet, EnumMap)에서 사용하도록 설계되었습니다. 이러한 목적이 아니라면 절대로 사용하지 않는 것이 좋습니다.

#### 결론

- 열거 타입의 상수와 관련된 값을 ordinal로 얻는 대신, 인스턴스 필드를 사용하여 열거 타입의 유연성과 유지보수성을 높이십시오.
