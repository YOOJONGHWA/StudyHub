## ITEM 12: overriding toString

## 예제: PhoneNumber 클래스

전화번호를 표현하는 간단한 클래스 toString 메서드를 재정의하여 객체의 주요 정보를 사람이 읽기 쉬운 형태로 반환하도록 구현합니다.

```java

public class PhoneNumber {
    private final int areaCode;      // 지역 코드
    private final int prefix;        // 국번
    private final int lineNumber;    // 가입자 번호

    // 생성자
    public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        if (areaCode < 0 || areaCode > 999)
            throw new IllegalArgumentException("유효하지 않은 지역 코드입니다.");
        if (prefix < 0 || prefix > 999)
            throw new IllegalArgumentException("유효하지 않은 국번입니다.");
        if (lineNumber < 0 || lineNumber > 9999)
            throw new IllegalArgumentException("유효하지 않은 가입자 번호입니다.");

        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
    }

    // toString 메서드 재정의
    @Override
    public String toString() {
        // 객체의 주요 정보를 사람이 읽기 쉬운 형태로 반환
        return String.format("(%03d) %03d-%04d", areaCode, prefix, lineNumber);
    }

    // 접근자 메서드 제공 (toString 내용의 정보를 개별적으로 제공)
    public int getAreaCode() {
        return areaCode;
    }

    public int getPrefix() {
        return prefix;
    }

    public int getLineNumber() {
        return lineNumber;
    }

    public static void main(String[] args) {
        // PhoneNumber 객체 생성
        PhoneNumber phoneNumber = new PhoneNumber(123, 456, 7890);

        // toString 호출
        System.out.println(phoneNumber); // (123) 456-7890

        // 다른 상황에서 자동 호출
        System.out.println(phoneNumber + "에 연결할 수 없습니다.");
        // 출력: (123) 456-7890에 연결할 수 없습니다.

        // 컬렉션에 포함된 객체 출력
        java.util.List<PhoneNumber> phoneBook = new java.util.ArrayList<>();
        phoneBook.add(phoneNumber);
        System.out.println(phoneBook); // [(123) 456-7890]
    }
}

```

#### 설명

1. toString 기본 구현

   - 객체의 주요 정보를 문자열로 표현해 디버깅이나 로그에서 직관적으로 확인할 수 있습니다.

2. 정보 제공 API

   - getAreaCode, getPrefix, getLineNumber 메서드를 통해 toString 결과의 개별 정보를 얻을 수 있도록 제공했습니다.

3. 자동 호출 활용

   - toString은 System.out.println, + 연산자, 컬렉션 출력 등에서 자동으로 호출되므로, 재정의하면 객체 정보를 유용하게 확인할 수 있습니다.

#### 유의 사항

- 요약 정보: 객체 상태가 너무 복잡하거나 거대할 경우 주요 정보만 포함하도록 설계해야 합니다.

- 정적 유틸리티 클래스: toString이 필요하지 않습니다.

- 열거 타입: 대부분의 경우 자바에서 제공하는 기본 toString이 적합합니다.

- 위 예제처럼 toString을 잘 정의하면 코드의 가독성과 디버깅 편의성이 크게 향상됩니다.
