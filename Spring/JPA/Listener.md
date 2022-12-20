# Entity Listener

* event발생하면 특정 동작 진행

* 종류 

  * @Pre/Post : 전/후
    * Persist : insert
    * Update : merge
    * Remove : delete
  * @PostLoad : select조회 직후

* 방법

  1. 직접 만들기

     1. EntityListener 작성

        @Pre/Post ~

        함수 작성

     2. dto에 EntityListener(value = EntityListener이름.class)

  2. 기존거 사용

     1. dto class 위에 EntityListener(value = AuditingEntityListener이름.class)
     2. dto 변수 위에 Annotation 추가
        * @CreatedDate : when create
        * @LastModifiedDate : when update
        * @CreatedBy : whoe create
        * @LastModifiedBy : who update

* 실습

  데이터 변경시간 추적하기

  데이터 변경 history 쌓기

* 실제 : dto변경 안하고 상속받음

  1. dto 작성

     ```java
     public enum Gender {
         MALE,
         FEMALE
     }
     ```

     ```java
     @Entity									// Entity 
     @NoArgsConstructor						// default 생성자
     @Data									// getter + setter
     @ToString(callSuper = true)				// 상위클래스 포함 toString
     @EqualsAndHashCode(callSuper = true)	// 상위클래스 포함 EqualsAndHashCode
     public class UserHistory extends BaseEntity {	
         @Id														// PK
         @GeneratedValue(strategy = GenerationType.IDENTITY)		// 자동증가
         private Long id;
     
         private String name;
     
         private String email;
     
         @Enumerated(value = EnumType.STRING)					// enum값 string 으로
         private Gender gender;
     }
     ```

  2. 상위클래스 작성 - listener 사용

     ```java
     @Data													// getter + setter
     @MappedSuperclass										// 상속받는 class의 column에 추가
     @EntityListeners(value = AuditingEntityListener.class)	// spring에 있는 EntityListeners 사용
     public class BaseEntity{
         @CreatedDate		// 생성시
         private LocalDateTime createdAt;
     
         @LastModifiedDate	// 변경시
         private LocalDateTime updatedAt;
     }
     ```

  3. repositoy interface 작성

     ```java
     public interface UserHistoryRepository extends JpaRepository<UserHistory, Long> {
     }
     ```

  4. 사용

     ```java
     @SpringBootTest		// 모든 Bean 가져옴
     class UserRepositoryTest {
         @Autowired
         private UserRepository userRepository;
         @Autowired		// Bean 채움
         private UserHistoryRepository userHistoryRepository;
     
         @Test
         void userHistoryTest() {
             // 생성
             User user = new User();
             user.setEmail("martin-new@fastcampus.com");
             user.setName("martin-new");
             userRepository.save(user);
     		
             // 수정
             user.setName("martin-new-new");
             userRepository.save(user);
     		
             // 출력
             userHistoryRepository.findAll().forEach(System.out::println);
         }
     }
     ```

