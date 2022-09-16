# 1. JPA(Java Persistence API)란?

Java가 종료되도 사라지지 않게 객체를 DB에 저장함

Java의 ORM 표준 스펙(자바의 class와 DB의 table을 매핑)을 정의

애플리케이션 - JDBC 사이에서 작동

# 2. Hibernate 란?

ORM Frameword 중 하나로, JPA 구현체



# 3. JPA 사용하는 이유

1. 객체 지향적(상속, 다형성, 관계)인 개발이 가능하다.
2. 생산성이 증가(간단한 메소드로 CRUD가 가능)
3. 유지보수가 쉽다(필드 변경 시 모든 SQL을 수정 안해도 됨)
4. Object와 RDB 간의 패러다임 불일치 해결



# 4. Persistence Container

객체를 생성한 프로그램이 종료되어도 사라지지 않게 저장하는 곳



# 3. 코드 작성 순서

1. dependency 설정

   * hibernate

   * jdbc

2. persistence.xml 작성

   * unit 이름, transaction type 
   * 필수 속성
     * jdbc driver
     * user 이름
     * user 비밀번호
     * url
   * 추가 속성(안해도 됨)
     * show sql - 실행된 sql문 보여주기
     * format - sql문 이쁘게 보여주기

3. 객체 class 작성

   * @Entity : JPA entity이다.
   * @Table : 매핑될 DB 테이블 이름
   * @Id : primary key
   * @Column : 객체의 속성과 DB의 column이름이 다를 때 매핑해줌

4. JPA 실행 코드 작성

   * EntityFactoryManager 생성 : 

     * EntityManager를 생성하기 위한 인터페이스 /
     * persistence.xml에서 작성한 unit이름 사용

   * EntityManager 생성  

     * Persistence context 관리

   * EntityTransaction 생성 

     * EntityManager에서 transaction을 가져와 관리

   * 실행(try - catch - fianal)

     * EntityTransaction 시작
       * entityTransaction.begin()
     * 생성자를 통해 객체 생성
     * Persist context에 추가
       * entityManager.persist(객체이름)
     * DB에 반영 
       * entityTransaction.commit()
     * 예외처리
       * 원인 출력
         * e.printStackTrace()
       * Persist context rollback
         * entityTransaction.rollback()

     * finally
       * EntityManager 닫기
         * entityManager.close()
     * EntityFactoryManager 닫기
       * entityFactoryManager.close()



# 4. 커스텀 EntityFactory

1. EntityFactory class 생성
   1. private으로 EntityFactory 개체 선언
   2. public으로 함수 만들기(개채 생성, entityManager 개채 생성, close)
2. main에서 성성한EntityFactory class호출 후 함수로 EntityFactory 개체 생성



# 5. 데이터 CRUD

## 1. Create 

![image-20220603171635817](../../AppData/Roaming/Typora/typora-user-images/image-20220603171635817.png)



## 2. Read

![image-20220603171729945](../../AppData/Roaming/Typora/typora-user-images/image-20220603171729945.png) 



## 3. Update

![image-20220603172259087](../../AppData/Roaming/Typora/typora-user-images/image-20220603172259087.png)



## 4. Delete

![image-20220603172441587](../../AppData/Roaming/Typora/typora-user-images/image-20220603172441587.png)



# 6. TypedQuery

![image-20220603172715798](../../AppData/Roaming/Typora/typora-user-images/image-20220603172715798.png)

SQL 의 경우는 return type만 빼기

단일 값만 하는 경우 - List<> userEntities = query.getSingleResultList();



## 7. find()



getReference()

