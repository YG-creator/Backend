# QueryMethod

1. repository에 함수에 사용할 QueryMethod 추가

   ```java
   public interface UserRepository extends JpaRepository<User, Long> {
       // where
       User findByEmail(String email);
   	
       // Is
       Set<User> findUserByNameIs(String name);
       Set<User> findUserByName(String name);
       Set<User> findUserByNameEquals(String name);
   	
       // limit
       List<User> findFirst2ByName(String name);
   
       List<User> findTop2ByName(String name);
   	
       // and
       List<User> findByEmailAndName(String email, String name);
   	
       // or
       List<User> findByEmailOrName(String email, String name);
   	
       // 비교
       List<User> findByCreatedAtAfter(LocalDateTime yesterday);
   
       List<User> findByIdAfter(Long id);
   
       List<User> findByCreatedAtGreaterThan(LocalDateTime yesterday);
   
       List<User> findByCreatedAtGreaterThanEqual(LocalDateTime yesterday);
   
       List<User> findByCreatedAtBetween(LocalDateTime yesterday, LocalDateTime tomorrow);
   
       List<User> findByIdBetween(Long id1, Long id2);
   
       List<User> findByIdGreaterThanEqualAndIdLessThanEqual(Long id1, Long id2);
   	
       // null
       List<User> findByIdIsNotNull();
   	
   	List<User> findByAddressIsNotEmpty();   // name is not null and name != '' ??
   	
       // in
       List<User> findByNameIn(List<String> names);
   	
       // like
       List<User> findByNameStartingWith(String name);
   
       List<User> findByNameEndingWith(String name);
   
       List<User> findByNameContains(String name);
   
       List<User> findByNameLike(String name);
   	
       // order by
       List<User> findTopByNameOrderByIdDesc(String name);
   
       List<User> findFirstByNameOrderByIdDescEmailAsc(String name);
   
       List<User> findFirstByName(String name, Sort sort);
   
       // paging
       Page<User> findByName(String name, Pageable pageable);
   }
   ```

   

2. 사용

   ```java
   void select() {
           System.out.println(userRepository.findByName("dennis"));
   
           System.out.println("findByEmail : " + userRepository.findByEmail("martin@fastcampus.com"));
           System.out.println("getByEmail : " + userRepository.getByEmail("martin@fastcampus.com"));
           System.out.println("readByEmail : " + userRepository.readByEmail("martin@fastcampus.com"));
           System.out.println("queryByEmail : " + userRepository.queryByEmail("martin@fastcampus.com"));
           System.out.println("searchByEmail : " + userRepository.searchByEmail("martin@fastcampus.com"));
           System.out.println("streamByEmail : " + userRepository.streamByEmail("martin@fastcampus.com"));
           System.out.println("findUserByEmail : " + userRepository.findUserByEmail("martin@fastcampus.com"));
   
           System.out.println("findSomethingByEmail : " + userRepository.findSomethingByEmail("martin@fastcampus.com"));
   
           System.out.println("findTop2ByName : " + userRepository.findTop2ByName("martin"));
           System.out.println("findFirst2ByName : " + userRepository.findFirst2ByName("martin"));
           System.out.println("findLast1ByName : " + userRepository.findLast1ByName("martin"));
   
           System.out.println("findByEmailAndName : " + userRepository.findByEmailAndName("martin@fastcampus.com", "martin"));
           System.out.println("findByEmailOrName : " + userRepository.findByEmailOrName("martin@fastcampus.com", "dennis"));
   
           System.out.println("findByCreatedAtAfter : " + userRepository.findByCreatedAtAfter(LocalDateTime.now().minusDays(1L)));
           System.out.println("findByIdAfter : " + userRepository.findByIdAfter(4L));
           System.out.println("findByCreatedAtGreaterThan : " + userRepository.findByCreatedAtGreaterThan(LocalDateTime.now().minusDays(1L)));
           System.out.println("findByCreatedAtGreaterThanEqual : " + userRepository.findByCreatedAtGreaterThanEqual(LocalDateTime.now().minusDays(1L)));
   
           System.out.println("findByCreatedAtBetween : " + userRepository.findByCreatedAtBetween(LocalDateTime.now().minusDays(1L), LocalDateTime.now().plusDays(1L)));
           System.out.println("findByIdBetween : " + userRepository.findByIdBetween(1L, 3L));
           System.out.println("findByIdGreaterThanEqualAndIdLessThanEqual : " + userRepository.findByIdGreaterThanEqualAndIdLessThanEqual(1L, 3L));
   
           System.out.println("findByIdIsNotNull : " + userRepository.findByIdIsNotNull());
   //        System.out.println("findByIdIsNotEmpty : " + userRepository.findByAddressIsNotEmpty());
   
           System.out.println("findByNameIn : " + userRepository.findByNameIn(Lists.newArrayList("martin", "dennis")));
   
           System.out.println("findByNameStartingWith : " + userRepository.findByNameStartingWith("mar"));
           System.out.println("findByNameEndingWith : " + userRepository.findByNameEndingWith("tin"));
           System.out.println("findByNameContains : " + userRepository.findByNameContains("art"));
   
           System.out.println("findByNameLike : " + userRepository.findByNameLike("%" + "art" + "%"));
       }
   
    @Test
       void pagingAndSortingTest() {
           System.out.println("findByNameWithPaging : " + userRepository.findByName("martin", PageRequest.of(1, 1, Sort.by(Order.desc("id")))).getTotalElements());
       }
   
   ```

   

## Select Query

1. select where

   A + B(선택) + By + 변수이름 + C

   * A : find, read, get, qeury, search, stream

   * B 

     * 타입 모를 때 : Something
     * 타입 알 때 : dto 타입
     * limit : First<number>, Top<number>

   * C

     * and : And

     * or : Or

     * 비교

       1. 초과

          GreaterThan

          After

       2. 미만

          LessThan

          Before

       3. 이상

          GreaterThanEqual

       4. 이하

          LessThanEqual

       5. 이상+이하

          Between

     * null

       IsNotNull

       IsNotEmpty : is not null and name != ''

     * in : In

     * like

       StartingWith() : x%

       EndingWith : %x

       Contains : %x%

       Like(%값%) : %x%

     * is

       Is = Equals = 공백

     * order by 

       내림차순 : OrderBy + 변수이름 + Desc 

       오른차순 : OrderBy + 변수이름 + Asc

       정렬기준 여러개인경우 : OrderBy + 속성이름1 + Desc/Asc + 속성이름2 + Desc/Asc 

       매개변수로 정렬하기 : findFirstByName("값", Sort.by(Order.desc("속성이름") x n)) : 가독성 좋아짐

2. 기타

* findAll() : 모두 출력

* findAll(Sort.by(Direction.DESC, "속성이름")) : 내림차순 정렬 후 모두 출력

  stream 사용하면 편함  -> .forEach(System.out::println)

* findAllById(Lists.newArrayList(PK값들)) : 한번에 여러개 찾기

* findById(PK값) : 조건에 맞는거 찾기

  findAllById는 직접 찾음

* getOne(PK값) : 조건에 맞는거 찾기

  getOne은 proxy한테 시킴 -> session 필요(@Transactional)

* 위 함수 뒤 + .orElse(null) : 존재하지 않으면 null 반환

* count() : 갯수

* existsById() : 존재하는지 확인 -> 결과는 count로 나옴



## Insert

* saveAll(Lists.newArrayLIst(인스턴스 여러개))

* save(인스턴스)



## Update

1. 찾기

   ```java
   User user = userRepository.findById("PK값").orElseThrow(RuntimeException::new);
   ```

2. set함수로 변경

   ```java
   user.setEmail("값");
   ```

3. save() 

   1. id값이 null이면 insert
   2. id값이 null이 아니면 update

   ```java
   userRepository.save(user);
   ```

   

## Delete

* delete(repository이름.findById(PK값).orElseThrow(RuntimeException::new))

  조건에 만족하는거 찾아서 지우기

  null이면 안되서 orElseThrow

* deleteById(PK값)

* deleteAll()  

  하나씩 찾아서 지움

* deleteAllInBatch() 

  안찾고 다 지움




## Commit

* flush() : commit



## Paging

* Page  

  slicing에 대한 정보, 전체 paging에대한 정보

  Paging에 대한 응답값

* Pageable : Paging 요청값

* Paging Query

  ```java
  System.out.println("findByNameWithPaging : " + userRepository.findByName("martin", PageRequest.of(1, 1, Sort.by(Order.desc("id")))).getTotalElements());
  ```

  1. 가져오기

     * Page<dto타입> 이름 = repository이름.findAll(PageREquest.of(원하는 페이지, 크기))

       페이지는 0부터 시작

  2. 출력

     * 이름

       페이지, ~의 instance인지

     * 이름.getTotalElements()

       총 record 갯수

     * 이름.getTotalPages()

       전체 페이지

     * 이름.getNumberOfElements()

       현재 가져온 record 갯수

     * 이름.getSort()

       정렬 여부

     * 이름.getSize()

       페이지 크기
