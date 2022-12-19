# ERD 사용법

draw.io 웹 어플리케이션 사용

1. 테이블(ER)
   * 테이블이름
   * 속성이름
   * PK, FK
2. 관계(화살표)
   * 1 :![image-20221217182022346](md-images/image-20221217182022346.png)
   * N :![image-20221217182011906](md-images/image-20221217182011906.png)
   * nullable : ![image-20221217181927182](md-images/image-20221217181927182.png)



# Dto 작성

```java
@Entity	// 테이블
@NoArgsConstructor	// default 생성자
@Data	// getter, setter
@ToString(callSuper = true)	// 상속변수 포함 toString
@EqualsAndHashCode(callSuper = true)	// 상속변수 포함 equals,hashcode
public class BookReviewInfo extends BaseEntity {
    @Id	// PK
    @GeneratedValue(strategy = GenerationType.IDENTITY)	// 자동증가
    private Long id;	

	// 그 외 속성변수
    
    // 관계, 연관관계있는 변수
}
```



## 관계

```java
// 1 : 1
@OneToOne

// 1 : N
@OneToMany

// N : 1 
@ManyToOne

// N : M
// 중간테이블 생성되서 많이 안씀
@ManyToOne
```



## 설정

```java
// 관계 기본속성
@관계(mappedBy = 'table 이름') // 조회는 되나 테이블 속성에는 없음
@관계(optional = false) // 조회는 되나 테이블 속성에는 없음   

// error 처리
@ToString.Exclude // 순환참조 -> stackOverFlow error
@Transactional // LazyInitializationException error
@JoinColumn(name = "author_id") // join 연산시 식별못하는 경우 처리
private List<dto타입> 이름 = new ArrayList<>(); // null 에러처리
```



## 주의사항

1. 변수타입

   1. primitive 타입 -> null이면 허용 -> 0

      ex) int

   2. reference 타입 -> null이면 허용안함 -> error

      ex) Integer

2. data.sql 자동증가 설정 

   hibernate 설정 지우기

3. N : M -> 1 : N, 1 : N

   자동으로 중간테이블이 생성이 되어서 컨트롤 못함 -> 중간테이블 직접 생성

   ```java
   @Entity
   @NoArgsConstructor
   @Data
   @ToString(callSuper = true)
   @EqualsAndHashCode(callSuper = true)
   public class Book extends BaseEntity {
   	@OneToMany
       @JoinColumn(name = "book_id")
       @ToString.Exclude
       private List<BookAndAuthor> bookAndAuthors = new ArrayList<>();
   }
   ```

   ```java
   @Entity
   @NoArgsConstructor
   @Data
   @ToString(callSuper = true)
   @EqualsAndHashCode(callSuper = true)
   public class Author extends BaseEntity {
   	@OneToMany
       @JoinColumn(name = "author_id")
       @ToString.Exclude
       private List<BookAndAuthor> bookAndAuthors = new ArrayList<>();
   }
   ```

   ```java
   @Entity
   @NoArgsConstructor
   @Data
   @ToString(callSuper = true)
   @EqualsAndHashCode(callSuper = true)
   public class BookAndAuthor extends BaseEntity {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
   
       @ManyToOne
       private Book book;
   
       @ManyToOne
       private Author author;
   }
   ```

