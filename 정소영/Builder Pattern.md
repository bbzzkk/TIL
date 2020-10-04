# Builder Pattern

> 쓰는 이유

인스턴스 생성시, 생성자만을 통해 생성하는 데에 어려움이 있다. 

- 인자가 많은 경우 어떤 인자가 어떤 값을 나타내는지 확인하기 힘듦.

- 점층적 생성자 패턴(필수인자를 받는 생성자 하나 정의하고, 선택적 인자를 받는 생성자를 하나씩 추가해가면서 정의하는 것. 즉, 오버로딩하여 생성자를 만드는 것 ) 사용시, 코드가 지저분해진다.

- setter메서드를 사용한 자바 빈 패턴 사용시, 함수 호출 1회로 객체 생성을 끝낼 수 없어 객체 일관성이 깨질 수 있음.

  :arrow_forward: 점층적 생성자 패턴 + 자바 빈 패턴 = 빌더 패턴

> 장점

1. 인스턴스 생성시, 필수적인 인자와 선택적 인자 구별 가능
2. 각 인자의 의미를 알 수 있다.
3. 객체의 일관성 유지(한번에 객체를 생성하기 때문)
4. setter메소드가 없으므로 변경 불가능 객체를 만들 수 있다.

> 예제코드

- 클래스 생성

```java
// 책 도메인 객체
public class Book {
    
    // Book의 필드
    private String title;
    private String author;
    private String publishing;
    private int price;
    
    public static class Builder {
        // Book과 동일한 필드를 갖는 Builder
        private String title;
        private String author;
        private String publishing;
        private int price;
        
        // 필드 별 Setter
        public Builder title(String title) {
            this.title = title;
            return this;
        }

        public Builder author(String author) {
            this.author = author;
            return this;
        }

        public Builder publishing(String publishing) {
            this.publishing = publishing;
            return this;
        }

        public Builder price(int price) {
            this.price = price;
            return this;
        }
        
        // Book을 반환하는 메소드
        public Book build() {
            return new Book(this);
        }
    }
    
    // Book 생성자
    public Book(Builder builder) {
        this.title = builder.title;
        this.author = builder.author;
        this.publishing = builder.publishing;
        this.price = builder.price;
    }
}
```

- 빌더를 이용한 객체 생성

```java
// Builder pattern 사용1
Book.Builder builder = new Book.Builder();
builder.title("채식주의자");
builder.author("한강");
builder.publishing("창비");
builder.price(12000);
Book book1 = builder.build();

// Builder pattern 사용2
Book book2 = new Book
        .Builder()
        .title("소년이 온다")
        .author("한강")
        .publishing("창비")
        .price(9900)
        .build();
```





