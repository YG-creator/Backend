# 요구사항 분석

1. 주소 입력 -> 정확한 주소

2. 정확한 주소 -> 위도, 경도 
3. 약국 현황 데이터 조회
4. 해당 주소 가장 가까운 약국 3개 출력
   * 위도,경도 사용
5. 결과물
   1. 약국명
   2. 약국 주소
   3. 거리(오름차순)
   4. 길안내 링크
   5. 로드뷰 링크




# 프로젝트 설계

## 약국 추천 과정

![스크린샷 2022-07-07 오후 1 58 39](https://user-images.githubusercontent.com/26623547/177694773-b53d1251-652f-41e6-8f19-c32b931d4b5b.png)



## Direction Shorten Url 과정

![스크린샷 2022-06-23 오후 9 42 58](https://user-images.githubusercontent.com/26623547/175301168-ee35793c-18ff-4a4a-8610-7a9455e9fef7.png)

## 구현할 기능

- Spring Data JPA를 이용한 CRUD 메서드 구현하기

- Spock를 이용한 테스트 코드 작성하기

- Testcontainers를 이용하여 독립 테스트 환경 구축하기

- 카카오 주소검색 API 연동하여 주소를 위도, 경도로 변환하기

- 추천 결과를 카카오 지도 URL로 연동하여 제공하기

- 공공 데이터를 활용하여 개발하기 (약국 현황 데이터)

- Handlebars를 이용한 간단한 View 만들기

- 도커를 사용하여 다중 컨테이너 애플리케이션 만들기

- 애플리케이션을 클라우드 서비스에 배포하기

- Spring retry를 이용한 재처리 구현하기 (카카오 API의 네트워크 오류 등에 대한 재처리)

- base62를 이용한 shorten url 개발하기 (길안내 URL)

- redis를 이용하여 성능 최적화하기




## 기술 스택

- JDK 11

- Spring Boot 2.6.7

- Spring Data JPA

- Gradle

- Handlebars : 프론트엔드

- Lombok

- Github

- Docker : 클라우드 서비스 배포

- AWS EC2 : 클라우드 서비스

- Redis

- MariaDB : 성능, 비용 고려 DB

- Spock : 테스트 코드

- Testcontainers : 독립적인 환경 테스트 코드 간편하게 작성 프레임워크

  

## 결과

![스크린샷 2022-06-23 오후 8 04 48](https://user-images.githubusercontent.com/26623547/175286356-deefcf5d-f94e-49c3-b0f0-4f2c2e3ed50a.png)

![스크린샷 2022-10-13 오후 10 30 04](https://user-images.githubusercontent.com/26623547/195610945-7bf03544-e8aa-44b9-ad54-090948c8d1ea.png)

