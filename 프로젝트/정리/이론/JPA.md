# 영속성 context

* 영속성 context 있는 이유

  1. 1차 캐시 
     * 1차 캐시에 이미 있는 값을 조회하는 경우 : DB 조회 안하고 1차 캐시에서 가져옴

  2. 쓰기 지연 SQL
     * flush를 해야 한번에 DB에 반영됨

  3. 엔티티 수정(Dirty checking)
     * 변화가 있는 entity는 쓰기 지연 SQL 저장소에 UDATE SQL 생성



# JPA Dirty Checking

1. 과정

   1. 영속성 context에 snapshot 저장
      * entity 상태 변경 검사 용도

   2. 변화가 있는 entity는 쓰기 지연 SQL 저장소에 UDATE SQL 생성

   3. flush 하면 쓰기 지연 SQL 저장소에 있는 쿼리를 DB에 반영



# 주의사항

1. 트랜잭션 밖에서 데이터 변경은 반영되지 않는다.
2. 영속 context에 있는 것만 변경 가능



# Spring JPA

1. 사용법
   1. dependency 추가
   2. entity 작성
   3. repository 작성
   4. service
      1. repository DI
      2. repository 메소드 사용



# JPA Auditing

* 사용 이유

  * 유지보수에 필요한 meta data 따로 모아둠 
  * 코드 반복 피하기 위해서 사용

* JPA Auditing이란?
  * @MappedSuperclass : 엔티티의 공통 매핑 정보
  * @EntityListeners : 해당 클래스에 auditing 기능을 포함
  * @CreatedDate : Entity가 생성된 시간을 자동 저장
  * @LastModifiedDate : Entity 변경될 때 시간 자동 update

* 사용법

  1. baseEntity 생성

     ```java
     @Getter
     @MappedSuperclass // JPA Entity 클래스들이 BaseTimeEntity을 상속할 경우 필드들 (createdDate, modifiedDate)도 컬럼으로 인식하도록 한다!
     @EntityListeners(AuditingEntityListener.class) // BaseTimeEntity 클래스에 Auditing 기능을 포함시킨다!
     public abstract class BaseTimeEntity {
     
         // Entity가 생성되어 저장될 때 시간이 자동 저장된다.
         @CreatedDate
         @Column(updatable = false) // 최초 저장 후 수정 금지
         private LocalDateTime createdDate;
     
         // 조회한 Entity의 값을 변경할 때 시간이 자동 저장된다.
         @LastModifiedDate
         private LocalDateTime modifiedDate;
     
         // @CreatedBy 생성자
         // @LastModifiedBy 수정자
     }
     ```

  2. 다른 entity에 상속

  3. application에 @EnableJpaAuditing