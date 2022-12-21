# @Embedded

ex) 가격정보, 주소정보

1. dto 생성, @Embedable

   ```java
   @Embeddable
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Address {
       private String city;    // 시
   
       private String district;    // 구
   
       @Column(name = "address_detail")
       private String detail;  // 상세주소
   
       private String zipCode; // 우편번호
   }
   ```

2. 사용하는 dto에 @Embedded

   속성 재정의 하고 싶으면 @AttributeOverride

   ```java
   public class User extends BaseEntity {
   	@Embedded
       @AttributeOverrides({
           @AttributeOverride(name = "city", column = @Column(name = "home_city")),
           @AttributeOverride(name = "district", column = @Column(name = "home_district")),
           @AttributeOverride(name = "detail", column = @Column(name = "home_address_detail")),
           @AttributeOverride(name = "zipCode", column = @Column(name = "home_zip_code"))
       })
       private Address homeAddress;
   }
   ```

   