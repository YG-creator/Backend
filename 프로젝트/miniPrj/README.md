# 실습

## DB CRUD

1. 환경설정 - webMVC, Lombok
2. db CRUD 
   1. CRUD 함수 interface  구현
   2. CRUD 구현하는 추상 class 구현
   3. db idx를 구현할 class 구현
   4. 실제 데이터가 들어갈 class 구현
3. db test







## 검색 by naver api

1. 고정된 입력데이터 저장(yml)
   1. uri
   2. id
   3. pwd
2. dto 만들기
   1. request
   2. response
3. Client 만들기
   1. 입력(@Value("${yml에 저장한거}"))
      1. uri
      2. id
      3. 비밀번호 
   2. 검색 기능
      1. uri
         1. 주소
         2. parameter
         3. 인코딩
         4. 빌드
         5. uri로 변환
      2. header
         1. 내용 넣기
         2. contentType 설정
      3. responseType 설정
      4. uri로 request 보내고 response 받기
         1. uri
         2. HTTP method
         3. 보낼거
         4. 받을 형식

4. 테스트



