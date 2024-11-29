#### ITEM 7: Eliminate Object Reference (불필요한 객체 참조 제거) 총정리

#### 1. 객체 참조 해제 (Memory Leak 방지)

자바에서 **가비지 컬렉터(GC)**가 메모리 관리를 자동으로 해주지만, 부주의한 객체 참조로 인해 GC가 더 이상 사용되지 않는 객체를 회수하지 못하는 **메모리 누수(Memory Leak)**가 발생할 수 있습니다. 이를 방지하려면 사용되지 않는 객체의 참조를 명시적으로 제거해 주어야 합니다.

#### 1.1 간단한 Stack 예제

문제: 객체를 더 이상 사용하지 않는데도, Stack 클래스에서는 배열에 참조를 남겨 놓고 있어 메모리 누수가 발생할 수 있습니다.

```java

public class Stack {
    private Object[] elements = new Object[10];
    private int size = 0;

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size]; // 다 쓴 객체 참조를 계속 유지함
    }
}

```

수정 방법:

pop() 메서드를 호출할 때, 참조를 꺼낸 후 배열에 null을 명시적으로 설정해 GC가 해당 객체를 회수할 수 있도록 합니다.

```java

public class Stack {
    private Object[] elements = new Object[10];
    private int size = 0;

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; // 참조 해제
        return result;
    }
}

```

#### 1.2 컬렉션에서의 문제

컬렉션(예: HashMap, ArrayList, Set)에서는 객체가 더 이상 사용되지 않더라도 내부에서 참조를 계속 유지하고 있을 수 있습니다. 이로 인해 GC가 해당 객체를 회수하지 못하게 되면 메모리 누수가 발생할 수 있습니다.

해결 방법:

명시적으로 참조를 null로 설정하거나
**WeakHashMap**을 사용하여 키가 더 이상 참조되지 않으면 자동으로 삭제되도록 할 수 있습니다.

```java

WeakHashMap<Integer, String> weakMap = new WeakHashMap<>();
Integer key = 1000;
weakMap.put(key, "value");
key = null;  // key가 null로 설정되면 해당 엔트리가 제거됨
System.gc();  // 강제로 GC 실행

```

#### 2. JPA 연관 관계 관리 (JPA Memory Leak 방지)

JPA에서 양방향 연관 관계를 관리할 때, 잘못된 참조 관리로 인해 객체들이 메모리에서 해제되지 않고 남아 있을 수 있습니다. 이를 방지하려면 JPA 엔티티 간의 참조를 적절히 끊어주어야 합니다.

2.1 양방향 연관 관계 문제 예시

```java

@Entity
public class Parent {
    @OneToMany(mappedBy = "parent")
    private List<Child> children = new ArrayList<>();
}

@Entity
public class Child {
    @ManyToOne
    private Parent parent;
}

```

위와 같은 양방향 관계에서 Parent 엔티티는 Child를 참조하고 있고, Child는 Parent를 참조하고 있습니다. 만약 연관 관계를 끊지 않으면 양쪽 엔티티가 서로 강하게 참조하게 되어 GC가 이를 회수하지 못해 메모리 누수가 발생할 수 있습니다.

#### 2.2 해결 방법: 연관 관계 메서드와 참조 해제

양방향 연관 관계에서는 반드시 참조를 관리해줘야 합니다. addChild(), removeChild()와 같은 메서드를 통해 양쪽의 참조를 일관되게 설정하거나 해제해야 합니다.

```java

public class Parent {
    @OneToMany(mappedBy = "parent", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Child> children = new ArrayList<>();

    public void addChild(Child child) {
        this.children.add(child);
        child.setParent(this);
    }

    public void removeChild(Child child) {
        this.children.remove(child);
        child.setParent(null);  // 부모 참조를 null로 설정하여 메모리 누수 방지
    }
}

```

2.3 orphanRemoval 설정
JPA에서는 orphanRemoval = true를 설정하면 부모 엔티티에서 자식 엔티티를 제거할 때 자동으로 자식 엔티티를 삭제합니다. 이를 통해 불필요한 객체들이 남아 있는 문제를 해결할 수 있습니다.

```java

@OneToMany(mappedBy = "parent", orphanRemoval = true)
private List<Child> children = new ArrayList<>();

```

#### 2.4 지연 로딩(Lazy Loading)과 프록시 문제

JPA는 연관된 엔티티를 **지연 로딩(Lazy Loading)**으로 로드할 때, 실제로 엔티티를 사용할 때까지 데이터를 로딩하지 않습니다. 이 과정에서 필요하지 않은 프록시 객체가 메모리에 남을 수 있기 때문에, 이를 관리해 주어야 합니다.

```java

@ManyToOne(fetch = FetchType.LAZY)
private Parent parent;

```

지연 로딩된 엔티티가 실제로 사용되지 않는다면, GC가 이를 회수할 수 있도록 적절한 타이밍에 초기화하거나, fetch 전략을 설정해야 합니다.

#### 요약

- 객체 참조 해제:

  - 사용하지 않는 객체는 명시적으로 null로 설정하거나 WeakHashMap과 같은 약한 참조를 사용해 GC가 회수할 수 있도록 합니다.

  - 컬렉션에서의 참조도 마찬가지로 다 사용한 객체를 null로 설정하거나 캐시 관리 전략을 적용합니다.

- JPA 연관 관계 관리:

  - 양방향 연관 관계에서 객체를 관리할 때는 연관 관계 메서드를 통해 서로의 참조를 일관되게 관리하고, 필요하면 orphanRemoval을 설정하여 자식 객체가 부모와의 관계에서 제거되면 자동으로 삭제되게 합니다.

  - 지연 로딩과 프록시 객체도 적절히 관리하여 메모리 낭비를 방지하고, 필요한 객체만 메모리에 로딩되도록 합니다.

이렇게 객체 참조와 연관 관계를 적절히 관리함으로써 메모리 누수를 예방하고 효율적인 메모리 관리를 할 수 있습니다.
