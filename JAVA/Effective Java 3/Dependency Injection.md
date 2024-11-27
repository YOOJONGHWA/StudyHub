#### ITEM 5: Dependency Injection

의존 객체 주입(Dependency Injection, DI)은 객체지향 설계에서 객체 간의 결합도를 낮추고, 유연성과 재사용성, 테스트 용이성을 높이는 중요한 패턴입니다. 이를 이해하기 위해 자동차와 엔진을 예로 들어보겠습니다.

1. 기본 개념
   의존 객체 주입은 객체가 자신의 의존성을 외부에서 주입받는 방식입니다.
   이를 통해 클래스는 직접 객체를 생성하지 않고, 필요한 자원(객체)을 외부에서 제공받습니다.
   이 방식은 코드의 결합도를 낮추고, 유연성과 재사용성, 테스트 용이성을 높여줍니다.

2. 의존 객체 주입이 없는 경우

```java

public class Car {
    private Engine engine;

    public Car() {
        this.engine = new Engine();  // 엔진을 직접 생성
    }

    public void start() {
        engine.start();
        System.out.println("차가 출발합니다.");
    }
}

```

Car 클래스가 Engine을 직접 생성하고 있습니다.
만약 엔진을 다른 종류로 바꾸려면 Car 클래스 내부를 수정해야 하므로 유연성이 부족하고, 테스트도 어렵습니다.

3. 의존 객체 주입을 사용한 경우

```java

public class Car {
    private final Engine engine;

    // 생성자에서 엔진을 주입받음
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
        System.out.println("차가 출발합니다.");
    }
}

```

이제 Car 클래스는 Engine 객체를 외부에서 주입받습니다.
따라서 다양한 종류의 엔진을 사용할 수 있어 유연성이 높아집니다.

4. 장점 정리

- 유연성

  - 자동차는 다양한 종류의 엔진을 사용할 수 있습니다.

  - 예를 들어, ElectricEngine, DieselEngine, GasolineEngine 등 엔진의 종류가 달라도 Car 클래스는 그대로 사용할 수 있습니다.

- 재사용성

  - Car 클래스는 특정 엔진에 의존하지 않기 때문에 다양한 상황에서 재사용 가능합니다.

  - 새로운 엔진을 추가하는 것도 Car 클래스를 수정하지 않고, 엔진만 추가하면 됩니다.

- 테스트 용이성
  - Car 클래스의 start 메소드를 테스트할 때, 실제 Engine 객체 대신 Mock 객체를 주입하여 테스트할 수 있습니다.

  - 예를 들어, ElectricEngine을 테스트하고 싶으면 ElectricEngine을 주입하고, DieselEngine을 테스트하려면 DieselEngine을 주입하는 방식으로 테스트를 쉽게 할 수 있습니다.

5. 클라이언트 코드 예시

```java

public class Main {
    public static void main(String[] args) {
        Engine electricEngine = new ElectricEngine();  // 전기 엔진
        Car car1 = new Car(electricEngine);  // 전기 엔진을 주입한 차
        car1.start();

        Engine dieselEngine = new DieselEngine();  // 디젤 엔진
        Car car2 = new Car(dieselEngine);  // 디젤 엔진을 주입한 차
        car2.start();
    }
}

```

Car 클래스를 생성할 때마다 다양한 엔진을 주입할 수 있습니다.
ElectricEngine을 주입한 Car와 DieselEngine을 주입한 Car를 각각 독립적으로 사용할 수 있습니다.

6. 의존 객체 주입의 단점과 해결책

- 단점: 의존성이 너무 많아지면 클래스가 복잡해질 수 있습니다.

- 해결책: DI 프레임워크(Spring 등)를 사용하여 의존 관계를 자동으로 관리하면, 복잡한 의존 관계를 효율적으로 처리할 수 있습니다.

7. 결론
   의존 객체 주입은 클래스 간의 결합도를 낮추고, 유연성, 재사용성, 테스트 용이성을 크게 향상시키는 방법입니다. 이를 통해 객체들이 서로 독립적으로 동작할 수 있도록 만들며, 시스템을 확장 가능하고 유지보수하기 쉬운 형태로 설계할 수 있습니다. DI 프레임워크를 활용하면, 이러한 이점을 더 효율적으로 관리할 수 있습니다.
