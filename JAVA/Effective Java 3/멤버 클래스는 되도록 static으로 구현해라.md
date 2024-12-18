## ITEM 24: 멤버 클래스는 되도록 static으로 구현해라

1. 정적 멤버 클래스
   정적 멤버 클래스는 바깥 클래스의 인스턴스와 독립적으로 존재하며, 주로 바깥 클래스와 함께 쓰이는 도우미 클래스로 사용됩니다.

- 예제:

```java

public class Calculator {
    // 정적 멤버 클래스
    public static class Operation {
        public int add(int a, int b) {
            return a + b;
        }
    }

    public static void main(String[] args) {
        // 바깥 클래스의 인스턴스 없이 정적 멤버 클래스 사용 가능
        Calculator.Operation operation = new Calculator.Operation();
        System.out.println("덧셈 결과: " + operation.add(5, 3));
    }
}

```

- Operation 클래스는 Calculator와 독립적입니다.

- static 키워드 덕분에 바깥 클래스 인스턴스 없이도 사용할 수 있습니다.

---

2. 비정적 멤버 클래스
   비정적 멤버 클래스는 바깥 클래스의 인스턴스와 강하게 연결됩니다.
   즉, 비정적 멤버 클래스는 반드시 바깥 클래스의 인스턴스를 통해 생성해야 합니다.

- 예제:

```java

public class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    // 비정적 멤버 클래스
    public class Greeting {
        public String greet() {
            return "안녕하세요, " + name + "님!";
        }
    }

    public static void main(String[] args) {
        Person person = new Person("홍길동");
        // 비정적 멤버 클래스 생성
        Person.Greeting greeting = person.new Greeting();
        System.out.println(greeting.greet());
    }
}

```

- Greeting 클래스는 바깥 클래스 Person의 name 필드에 접근할 수 있습니다.

- 바깥 클래스의 인스턴스를 통해 생성해야 합니다.

---

3. 익명 클래스
   익명 클래스는 이름이 없으며, 보통 인터페이스나 추상 클래스를 구현할 때 사용됩니다. 선언과 동시에 인스턴스를 생성합니다.

- 예제:

```java

public class AnonymousExample {
    interface Operator {
        int operate(int a, int b);
    }

    public static void main(String[] args) {
        // 익명 클래스
        Operator addition = new Operator() {
            @Override
            public int operate(int a, int b) {
                return a + b;
            }
        };

        System.out.println("덧셈 결과: " + addition.operate(10, 20));
    }
}

```

- 익명 클래스는 Operator 인터페이스를 구현하며, 선언과 동시에 인스턴스를 생성합니다.

- 한 번만 사용할 클래스일 때 유용합니다.

---

4. 지역 클래스
   지역 클래스는 메서드 내에서 선언되며, 해당 메서드에서만 사용 가능합니다. 이름이 있어 반복 사용이 가능합니다.

- 예제:

```java

public class LocalExample {
    public void printMessage(String message) {
        // 지역 클래스
        class Printer {
            public void print() {
                System.out.println("메시지: " + message);
            }
        }

        Printer printer = new Printer();
        printer.print();
    }

    public static void main(String[] args) {
        LocalExample example = new LocalExample();
        example.printMessage("안녕하세요!");
    }
}

```

- Printer는 printMessage 메서드 안에서만 사용됩니다.

- 메서드의 지역 변수 message를 참조할 수 있습니다.

---

#### 결론

- 정적 멤버 클래스: 바깥 클래스와 독립적으로 사용해야 할 때.

- 비정적 멤버 클래스: 바깥 클래스의 인스턴스와 강한 연관이 있을 때.

- 익명 클래스: 한 번만 사용되는 간단한 구현이 필요할 때.

- 지역 클래스: 메서드 내에서만 사용되며 이름이 필요한 경우.
