# Docker 탄생 배경

상황

1. 클라우드로 인해 적은 수의 서버 관리가 쉬워짐

2. 그러나 여러개의 서버 관리가 어려움

결과로 Docker 등장

1. OS와 서비스 운영환경 분리
2. 운영환경을 이미지로 관리
3. Container사용으로 VM보다 가벼움



# Docker 장점

* 편리한 관리

  배포하기 위해 이미지만 관리하면 됨

* 확장

  이미지 하나로 여러 개의 환경 생성 가능

* 테스트

  이미지로 서비스 환경과 개발 환경을 구성하기 쉬움

* 가벼움

  호스트 OS와 서비스 운영을 분리 + VM보다 가벼움



# Docker vs VM

Docker : Server + 호스트 OS + Docker + 애플리케이션 x n

VM : Server + 호스트 OS + Hypervisor + (게스트 OS + 애플리케이션) x n



게스트OS없이 호스트 OS를 공유함 -> 오버헤들 줄임 + SW 지식없이 쉽게 사용 가능 + 이미지 팡리 용량 적음



# Docker 아키텍처

1. Docker Client

   사용자의 명령을 입력하면 Docker Daemon에게 Docker API를 통해 전달

2. Docker Daemon

   Docker Client의 Docker API를 통해 전달받고 Docker 객체를 관리

3. Docker Registry

   Docker 이미지를 저장하는 공간



# Docker 객체

1. Dockerfile

   빌드하면 이미지가 생김

2. 이미지

   서비스 운영 환경 모음집

3. 컨테이너

   Docker 이미지를 실행하는 객체 or 이미지를 실행한 상태

   실행하는데 필요한 모든 구성요소를 담고 있어서 호스트 OS에 의존할 필요가 없음

   쉽게 공유 가능

   동일한 개발 환경 가질 수 있음



# Docker 기본 명령어

https://programforlife.tistory.com/77



# DockerFile 다루기

https://programforlife.tistory.com/84

https://programforlife.tistory.com/85



# Docker Image 다루기

https://programforlife.tistory.com/82

https://programforlife.tistory.com/83



# Docker Compose 다루기

하나의 애플리케이션을 여러 개의 Docker로 구성하는 방법

컨테이너 간의 연결을 구성하고 실행하는 방법

Docker.yaml 파일에 작성해서 구성함

https://programforlife.tistory.com/86