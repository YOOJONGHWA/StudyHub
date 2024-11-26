#### item 4. 인스턴스화를 막으려거든 private 생성자를 사용하라 Enforce noninstantiability with a private constuctor

#### 기본적인 유틸리티 클래스 (잘못된 예)

```java

public class ImageUtility {
    private static String IMAGE_DATE_FORMAT = "yyyyMMddHHmm";

    public static String makeImageFileNm(String imgFileNm) {
        return imgFileNm + "_" + new SimpleDateFormat(IMAGE_DATE_FORMAT).format(new Date());
    }
}

```

위 코드에서 ImageUtility 클래스는 정적 메서드만 존재하는 유틸리티 클래스입니다. 하지만 생성자가 명시되어 있지 않으면 컴파일러가 자동으로 기본 생성자(인수 없이 객체를 생성할 수 있는 생성자)를 생성해줍니다. 그러면 잘못된 사용이 발생할 수 있습니다.

#### 잘못된 사용 예시

```java

ImageUtility utility = new ImageUtility();  // 잘못된 인스턴스화
String imageFileNm = utility.makeImageFileNm("test");  // 불필요한 객체 생성


```

#### private 생성자 추가하여 인스턴스화 방지

인스턴스화를 막기 위해 private 생성자를 추가

이를 통해 객체를 생성할 수 없게 만들수 있다.

```java

public class ImageUtility {
    private static String IMAGE_DATE_FORMAT = "yyyyMMddHHmm";

    // 생성자를 private으로 설정하여 인스턴스화 방지
    private ImageUtility() {
        throw new AssertionError("Cannot instantiate ImageUtility");
    }

    // 정적 메서드
    public static String makeImageFileNm(String imgFileNm) {
        return imgFileNm + "_" + new SimpleDateFormat(IMAGE_DATE_FORMAT).format(new Date());
    }
}


```

#### 변경 사항:

- private 생성자를 추가하여 클래스 외부에서 인스턴스를 생성할 수 없도록 했습니다.

- 생성자가 호출되면 AssertionError를 던져서 실수로 인스턴스를 만들지 않도록 경고합니다.

- 이제 정적 메서드만 사용하게 됩니다.

##### 올바른 사용 예시:

```java

String imageFileNm = ImageUtility.makeImageFileNm("test");  // 객체 생성 없이 메서드 사용

```

이렇게 ImageUtility 클래스는 정적 메서드만 사용할 수 있도록 하고, 불필요한 객체 생성을 방지할 수 있습니다.

#### 추상 클래스와 private 생성자의 차이점

추상 클래스로 인스턴스를 방지할 수도 있지만, private 생성자와는 다르게 상속을 통해 인스턴스를 생성할 수 있습니다.

```java

abstract class ImageUtility {
    private static String IMAGE_DATE_FORMAT = "yyyyMMddHHmm";

    public static String makeImageFileNm(String imgFileNm) {
        return imgFileNm + "_" + new SimpleDateFormat(IMAGE_DATE_FORMAT).format(new Date());
    }
}

public class ItemImageUtility extends ImageUtility {
    // 상속을 통해 ImageUtility의 메서드 사용
}


```

#### final 클래스와 private 생성자

final 클래스를 사용하면 상속이 불가능하지만, 여전히 인스턴스를 만들 수 있는 기본 생성자가 자동으로 생성될 수 있습니다. 따라서, private 생성자를 사용해 인스턴스화를 방지해야 합니다.

```java

public final class Math {
    // Math 클래스의 인스턴스화 방지
    private Math() {}

    // 정적 메서드들
    public static double sin(double a) {
        return StrictMath.sin(a);
    }
}

```

이와 같이 final 클래스를 만들고 private 생성자를 선언함으로써 클래스의 인스턴스를 아예 만들지 못하게 할 수 있습니다.

#### 정리

- private 생성자를 사용하면 인스턴스화 방지가 가능하고, 정적 메서드만 제공하는 유틸리티 클래스를 만들 수 있습니다.

- 추상 클래스나 final 클래스는 상속을 통해 객체 생성이 가능하지만, private 생성자를 추가하면 클래스의 인스턴스를 아예 생성할 수 없게 됩니다.

- private 생성자는 클래스 외부에서 인스턴스를 만들지 못하도록 하며, 클래스 내부에서 발생할 수 있는 실수를 방지하는 데 유용합니다.

- 이 방식은 유틸리티 클래스나 상수, 정적 메서드만 제공하는 클래스를 설계할 때 주로 사용됩니다.

  - java.lang.Math

    ```java

    public class Arrays {

    /**
        * The minimum array length below which a parallel sorting
        * algorithm will not further partition the sorting task. Using
        * smaller sizes typically results in memory contention across
        * tasks that makes parallel speedups unlikely.
        */
    private static final int MIN_ARRAY_SORT_GRAN = 1 << 13;

    // Suppresses default constructor, ensuring non-instantiability.
    private Arrays() {}

    ```

  - java.util.Arrays

    ```java

    public final class Math {

    /**
    * Don't let anyone instantiate this class.
    */
    private Math() {}

    ```

![alt text](<img/napkin-selection%20(3).png>)
