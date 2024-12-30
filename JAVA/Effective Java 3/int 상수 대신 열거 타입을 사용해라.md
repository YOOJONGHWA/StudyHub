## ITEM 34: int 상수 대신 열거 타입을 사용하라

- 열거 타입을 사용하면 기존 int 상수를 사용하는 방식보다 타입 안전성, 코드 가독성, 유지보수성이 향상됩니다.

---

#### 1. 상수란?

- 상수는 변하지 않는 값을 의미합니다. 예를 들어, 원주율(π), 지구의 중력가속도 등은 모두 상수입니다.

```java

public static final int MONDAY = 1;
public static final int TUESDAY = 2;
public static final int WEDNESDAY = 3;

```

- 이 방식은 간단해 보이지만, 여러 가지 문제점이 있습니다.

---

#### 2. 정수 열거 패턴의 문제점

- (1) 이름 충돌 문제
  - 상수 이름을 구분하기 위해 접두어를 붙여야 합니다.

```java

public static final int APPLE_FUJI = 0;
public static final int ORANGE_NAVEL = 0;

```

- 하지만 이렇게 하면 APPLE_FUJI == ORANGE_NAVEL이 true로 평가될 수 있어, 의미가 모호해집니다.

- (2) 타입 안전성 부족

  - 상수를 사용하면, 컴파일러가 타입 오류를 검출할 수 없습니다.

```java

public void printDay(int day) { ... }

printDay(APPLE_FUJI); // 컴파일러가 오류를 잡지 못함

```

- (3) 값 변경 시의 문제

  - 정수 상수의 값이 변경되면, 해당 상수를 사용하는 모든 코드를 다시 컴파일해야 합니다.

- (4) 가독성 부족
  - 정수 값만으로는 상수의 의미를 파악하기 어렵습니다.

```java

System.out.println(MONDAY); // 1

```

#### 3. 열거 타입이란?

- 열거 타입은 상수를 안전하고 편리하게 사용할 수 있도록 도와줍니다.

```java

public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

```

- (1) 열거 타입의 특징

  - 열거 타입은 클래스이며, 상수는 열거 타입의 인스턴스입니다.

  - 각 상수는 고유한 이름과 값으로 정의되며, 다른 값을 허용하지 않습니다.

  - 타입 안전성을 제공합니다. 즉, 잘못된 값이 사용되면 컴파일 오류가 발생합니다.

#### 4. 열거 타입의 장점

- (1) 타입 안전성

```java

public void printDay(Day day) { ... }

printDay(Day.MONDAY); // OK
printDay(1); // 컴파일 오류

```

- (2) 가독성 향상

  - 상수 이름을 출력할 수 있습니다.

```java

System.out.println(Day.MONDAY); // MONDAY

```

- (3) 값 변경에 유연

- 열거 타입은 내부적으로 관리되므로, 상수 값이 변경되더라도 클라이언트 코드를 다시 컴파일할 필요가 없습니다.

- (4) 추가 기능 구현 가능

  - 열거 타입에 메서드나 필드를 추가할 수 있습니다.

---

#### 5. 실제 사용 예제

- (1) 기본 열거 타입

```java

public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

```

- (2) 상수에 데이터 추가

```java

public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6);

    private final double mass;   // 질량
    private final double radius; // 반지름

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    public double surfaceGravity() {
        final double G = 6.67430e-11;
        return G * mass / (radius * radius);
    }
}

```

- (3) 상수별 메서드 구현

```java

public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }

    public abstract double apply(double x, double y);
}

```

#### 사용 예시

```java

public class Calculator {
    public static void main(String[] args) {
        double x = 10;
        double y = 5;

        for (Operation op : Operation.values()) {
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
        }
    }
}



// 출력 결과


10.000000 + 5.000000 = 15.000000
10.000000 - 5.000000 = 5.000000
10.000000 * 5.000000 = 50.000000
10.000000 / 5.000000 = 2.000000

```

#### 6. 요약

- 정수 상수 대신 열거 타입을 사용하세요.

- 열거 타입은 타입 안전성, 가독성, 유지보수성을 제공합니다.

- 열거 타입에 데이터와 메서드를 추가해 객체 지향적으로 설계할 수 있습니다.
