#### ITEM 2: Builder Pattern

1. 점층적 생성자 패턴 (Telescoping Constructor Pattern)

점층적 생성자 패턴은 여러 생성자를 통해 객체를 생성하지만, 선택적 속성이 많을 경우 생성자가 많아져 코드가 복잡해지고 가독성이 떨어집니다. 생성자 수가 많아지면서 유지보수도 어려워집니다.

- 예시

```java

public class Pizza {
    private final String size; // 필수
    private final boolean cheese; // 선택
    private final boolean pepperoni; // 선택
    private final boolean mushrooms; // 선택

    public Pizza(String size) {
        this(size, false, false, false);
    }

    public Pizza(String size, boolean cheese) {
        this(size, cheese, false, false);
    }

    public Pizza(String size, boolean cheese, boolean pepperoni) {
        this(size, cheese, pepperoni, false);
    }

    public Pizza(String size, boolean cheese, boolean pepperoni, boolean mushrooms) {
        this.size = size;
        this.cheese = cheese;
        this.pepperoni = pepperoni;
        this.mushrooms = mushrooms;
    }
}


```

- 문제점

- 선택적 속성이 많을수록 생성자 수가 늘어나고, 코드가 복잡해짐

- 가동석이 떨어지고, 유지보수가 어려워짐

---

2. 자바빈즈 패턴 (JavaBeans Pattern)

자바빈즈 패턴은 기본 생성자와 setter 메서드를 사용하지만, 객체가 완전히 설정되기 전에 불완전한 상태일 수 있어 일관성 문제가 발생할 수 있습니다. 특히 setter 호출 순서에 따라 객체가 예상과 다르게 구성될 수 있습니다.

- 예시

```java

public class Pizza {
    private String size; // 필수
    private boolean cheese; // 선택
    private boolean pepperoni; // 선택
    private boolean mushrooms; // 선택

    public Pizza() {}

    public void setSize(String size) { this.size = size; }
    public void setCheese(boolean cheese) { this.cheese = cheese; }
    public void setPepperoni(boolean pepperoni) { this.pepperoni = pepperoni; }
    public void setMushrooms(boolean mushrooms) { this.mushrooms = mushrooms; }
}


```

- 문제점

- 객체가 완전히 설정되기 전에 불완전한 상태일 수 있음.

- setter 메서드 호출 순서가 중요하며, 객체의 불변성이 보장되지 않음.

- 실수로 불완전한 객체가 생성될 위험 있음.

---

3. 빌더 패턴 (Builder Pattern)

빌더 패턴은 선택적 속성이 많은 객체 생성 시 가독성과 안정성을 보장합니다. 필수 매개변수는 생성자로 받고, 선택적 매개변수는 메서드 체이닝 방식으로 설정할 수 있습니다. 빌더 패턴은 객체 생성 시 복잡한 설정을 분리하여 더 나은 코드 가독성을 제공합니다.

```java

public class Pizza {
    private final String size;  // 필수
    private final boolean cheese; // 선택
    private final boolean pepperoni; // 선택
    private final boolean mushrooms; // 선택

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.mushrooms = builder.mushrooms;
    }

    public static class Builder {
        private final String size; // 필수
        private boolean cheese = false;  // 기본값
        private boolean pepperoni = false;  // 기본값
        private boolean mushrooms = false;  // 기본값

        public Builder(String size) {
            this.size = size;
        }

        public Builder cheese(boolean cheese) {
            this.cheese = cheese;
            return this;
        }

        public Builder pepperoni(boolean pepperoni) {
            this.pepperoni = pepperoni;
            return this;
        }

        public Builder mushrooms(boolean mushrooms) {
            this.mushrooms = mushrooms;
            return this;
        }

        public Pizza build() {
            return new Pizza(this);
        }
    }

    @Override
    public String toString() {
        return "Pizza [size=" + size + ", cheese=" + cheese + ", pepperoni=" + pepperoni + ", mushrooms=" + mushrooms + "]";
    }
}

// 사용 예시
Pizza pizza = new Pizza.Builder("Large")
                    .cheese(true)
                    .pepperoni(true)
                    .build();

System.out.println(pizza);


```

---

4. 빌더 패턴에 스태틱 메서드 추가

스태틱 메서드를 추가하여 기본값을 설정한 객체를 더 쉽게 생성할 수 있습니다. 자주 사용되는 객체를 스태틱 메서드를 통해 간단히 만들 수 있게 됩니다.

```java

public class Pizza {
    private final String size;  // 필수
    private final boolean cheese; // 선택
    private final boolean pepperoni; // 선택
    private final boolean mushrooms; // 선택

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.mushrooms = builder.mushrooms;
    }

    public static class Builder {
        private final String size; // 필수
        private boolean cheese = false;  // 기본값
        private boolean pepperoni = false;  // 기본값
        private boolean mushrooms = false;  // 기본값

        public Builder(String size) {
            this.size = size;
        }

        public Builder cheese(boolean cheese) {
            this.cheese = cheese;
            return this;
        }

        public Builder pepperoni(boolean pepperoni) {
            this.pepperoni = pepperoni;
            return this;
        }

        public Builder mushrooms(boolean mushrooms) {
            this.mushrooms = mushrooms;
            return this;
        }

        public Pizza build() {
            return new Pizza(this);
        }
    }

    // 스태틱 메서드 추가
    public static Pizza createCheesePizza(String size) {
        return new Builder(size)
                .cheese(true)
                .build();
    }

    public static Pizza createPepperoniPizza(String size) {
        return new Builder(size)
                .pepperoni(true)
                .build();
    }

    public static Pizza createMushroomPizza(String size) {
        return new Builder(size)
                .mushrooms(true)
                .build();
    }

    @Override
    public String toString() {
        return "Pizza [size=" + size + ", cheese=" + cheese + ", pepperoni=" + pepperoni + ", mushrooms=" + mushrooms + "]";
    }
}

Pizza cheesePizza = Pizza.createCheesePizza("Large");
Pizza pepperoniPizza = Pizza.createPepperoniPizza("Medium");
Pizza mushroomPizza = Pizza.createMushroomPizza("Small");

System.out.println(cheesePizza);  // Pizza [size=Large, cheese=true, pepperoni=false, mushrooms=false]
System.out.println(pepperoniPizza);  // Pizza [size=Medium, cheese=false, pepperoni=true, mushrooms=false]
System.out.println(mushroomPizza);  // Pizza [size=Small, cheese=false, pepperoni=false, mushrooms=true]


```

- 스태틱 메서드의 장점

- 편리한 객체 생성: 자주 사용되는 설정을 미리 정의하고, 해당 설정을 손쉽게 사용할 수 있음.

- 명확한 의도: 기본적으로 설정된 객체를 쉽게 생성할 수 있어, 코드의 의도가 명확하게 전달됨.

- 불변성 유지: 스태틱 메서드를 통해 생성된 객체는 여전히 불변성을 보장하며, 생성 후에는 수정이 불가능.

---

5. 결론

빌더 패턴은 선택적 속성이 많은 객체를 만들 때 매우 유용합니다. 이를 통해 가독성과 안정성을 높일 수 있습니다. 또한 스태틱 메서드를 추가하면 자주 사용되는 기본값 설정을 쉽게 적용할 수 있어 객체 생성이 더욱 편리해집니다. 빌더 패턴과 스태틱 메서드를 결합하여 더 명확하고 유연한 코드를 작성할 수 있습니다.

![alt text](/JAVA/Effective%20Java%203/img/napkin-selection.png)
