## ITEM 13: overriding clone judiciously

clone() 메서드는 객체 복사를 위한 기능을 제공합니다. 하지만 이를 올바르게 재정의하지 않으면 원본 객체와 복사본이 서로 예상치 못하게 영향을 주는 문제가 생길 수 있습니다. 특히, 복제본이 원본의 내부 데이터를 공유할 경우 이런 문제가 자주 발생합니다.

#### 예제: 책장 복사

- 잘못된 clone() 예제

1. 책장을 표현하는 BookShelf 클래스가 있다고 가정합니다.

2. 책장 안에는 책들이 배열로 저장되어 있습니다.

3. 단순히 super.clone()을 사용해 복제했을 때, 복제된 책장이 원본과 같은 책 배열을 공유한다면 문제가 발생합니다.

```java

class BookShelf implements Cloneable {
    private String[] books;

    public BookShelf(String[] books) {
        this.books = books;
    }

    public void addBook(int index, String book) {
        books[index] = book;
    }

    public String[] getBooks() {
        return books;
    }

    @Override
    public BookShelf clone() {
        try {
            return (BookShelf) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError(); // 이 예외는 발생하지 않음
        }
    }
}

// 테스트 코드
String[] books = {"Book1", "Book2"};
BookShelf shelf1 = new BookShelf(books);
BookShelf shelf2 = shelf1.clone();

// 복제된 책장에 새로운 책 추가
shelf2.addBook(0, "New Book");

// 원본 책장에도 영향을 줌
System.out.println(Arrays.toString(shelf1.getBooks())); // [New Book, Book2]

```

#### 문제점

clone() 메서드에서 배열이 복사되지 않고 참조가 공유되었습니다. 복제된 책장 shelf2에서 배열을 변경하면, 원본 책장 shelf1에도 영향을 줍니다.

---

#### 올바른 clone() 예제

원본 책장과 복제된 책장이 각각 독립적인 배열을 가지도록 하려면, 배열을 깊은 복사해야 합니다.

```java

class BookShelf implements Cloneable {
    private String[] books;

    public BookShelf(String[] books) {
        this.books = books;
    }

    public void addBook(int index, String book) {
        books[index] = book;
    }

    public String[] getBooks() {
        return books;
    }

    @Override
    public BookShelf clone() {
        try {
            BookShelf cloned = (BookShelf) super.clone();
            // 배열을 깊은 복사
            cloned.books = books.clone();
            return cloned;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}

// 테스트 코드
String[] books = {"Book1", "Book2"};
BookShelf shelf1 = new BookShelf(books);
BookShelf shelf2 = shelf1.clone();

// 복제된 책장에 새로운 책 추가
shelf2.addBook(0, "New Book");

// 원본 책장은 영향을 받지 않음
System.out.println(Arrays.toString(shelf1.getBooks())); // [Book1, Book2]
System.out.println(Arrays.toString(shelf2.getBooks())); // [New Book, Book2]

```

#### 올바른 점

1. clone() 메서드에서 books 배열을 복사하여 원본과 복제본이 독립적으로 동작하도록 구현했습니다.

2. 복제된 객체가 원본 객체와 데이터를 공유하지 않으므로 예상치 못한 동작이 없습니다.

---

#### 핵심 정리

- clone() 메서드를 재정의할 때는 깊은 복사가 필요할 수 있습니다.

- 객체의 내부 상태(특히 가변 객체 참조)가 원본과 복제본에서 공유되지 않도록 주의해야 합니다.

- 배열과 같은 가변 데이터를 복사하려면, clone()이나 수동으로 복사를 수행해야 합니다.

- 더 나은 대안으로는 복사 생성자나 복사 팩터리를 사용하는 것이 일반적입니다.
