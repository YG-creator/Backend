# 1. 필요성

* Query method custom version

1. 가독성
   * 함수이름 길어지는 경우 필요함

2. 편의성
   * 다른 Entity 사용 가능
   * 페이징 가능
3. 성능
   * JPA는 보통 update, delete 시 하나씩 조회해서 처리함 -> Native Query로 이를 방지





# 2. JPQL

repository에 @Query(value = "jpql") 추가해서 custom

1. 매개변수 순서 ver

   ```java
   @Query(value = "select b from Book b "
         + "where 속성이름 = ?1 and 속성이름 >= ?2");
   타입 함수이름(타입 변수1, 타입 변수2);
   ```

2. 매개변수 이름 ver(권장)

   ```java
   @Query(value = "select b from Book b "
         + "where 속성이름 = :변수이름1 and 속성이름 >= 변수이름2");
   타입 함수이름(@Param("변수이름1") 타입 변수1, @Param("변수이름2") 타입 변수2);
   ```

3. 다른 Entity 사용

   ```java
   @Query(value = "select new com.fastcampus.jpa.bookmanager.repository.dto.BookNameAndCategory(b.name, b.category) from Book b")
   List<BookNameAndCategory> findBookNameAndCategory();
   ```

4. Paging

   ```java
   @Query(value = "select new com.fastcampus.jpa.bookmanager.repository.dto.BookNameAndCategory(b.name, b.category) from Book b")
   Page<BookNameAndCategory> findBookNameAndCategory(Pageable pageable);
   ```

   

   

# 3. Native Query

## Native Query란?

* JPQL 대신 SQL 사용하기
* JPA는 보통 update, delete 시 하나씩 조회해서 처리함 -> Native Query로 이를 방지



## 사용법

* repository에 추가

  ```java
  @Query(value = "select * from book", nativeQuery = true)
      List<Book> findAllCustom();
  
  // update인 경우 
  @Transactional	
  @Modifying		
  @Query(value = "update book set category = 'IT전문서'", nativeQuery = true)
  int updateCategories();
  ```

  

## 주의점

* @Where 적용 안됨



# 4. Converter

## Converter란?

* ORM custom



## 사용법

1. dto 생성

   ```java
   @Data
   public class BookStatus {
       private int code;
       private String description;
   
       public BookStatus(int code) {
           this.code = code;
           this.description = parseDescription(code);
       }
   
       public boolean isDisplayed() {
           return code == 200;
       }
   
       private String parseDescription(int code) {
           switch (code) {
               case 100:
                   return "판매종료";
               case 200:
                   return "판매중";
               case 300:
                   return "판매보류";
               default:
                   return "미지원";
           }
       }
   }
   ```

2. converter 생성

   ```java
   @Converter(autoApply = true)	// autoApply=true시 @Convert할 필요없음
   public class BookStatusConverter implements AttributeConverter<BookStatus, Integer> {
       @Override
       public Integer convertToDatabaseColumn(BookStatus attribute) {
           return attribute.getCode();
       }
   
       @Override
       public BookStatus convertToEntityAttribute(Integer dbData) {
           return dbData != null ? new BookStatus(dbData) : null;
       }
   }
   ```

3. converter 적용

   ```java
   // 다른 dto
   @Entity
   @NoArgsConstructor
   @Data
   @ToString(callSuper = true)
   @EqualsAndHashCode(callSuper = true)
   public class Book extends BaseEntity {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
   	
       // 추가
       @Convert(converter = BookStatusConverter.class)	// autoconverter 사용시 없애도 됨
       private BookStatus status; // 판매상태
   }
   ```

   

   

## 주의점

1. 정방향, 역방향 둘다 구현해야 됨

2. autoapply는 개발자가 생성한 class에 한해서 사용해야 됨





# 기타

@Column 기본속성

* columnDefinition : 데이터타입 + default값 + 설명
* nullable
* updatable



