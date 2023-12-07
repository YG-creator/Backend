# 개념

* Docker란?
  * 컨테이너를 사용하여 응용프로그램을 더 쉽게 배포하고 실행할 수 있도록 설계된 도구

* Docker를 사용하는 이유

  * 서버마다 동일한 환경을 구성 -> 환경설정으로 인한 문제 해결
  * auto scaling에 유리

* Docker vs 하이퍼 바이저(VM)

  ![image-20230203164945256](C:\Users\YG\Desktop\Backend\프로젝트\약국\md-images\image-20230203164945256.png)

  * 하이퍼 바이저
    * 호스트 시스템에서 다수의 OS를 구동할 수 있게 하는 소프트웨어 -> App 여러개 실행 가능
    * 각 VM마다 독립적
  * Docker
    * 경량화된 하이퍼 바이저 구조(VM+OS -> Container)
    * App 하나에 error가 생겨도 전파 되지 않음(container 당 1개의 App을 실행해서)

* Container

  * 어플리케이션 환경에 구애 받지 않고 손쉽게 배포 관리를 할 수 있게 해준다.
  * Docker Image를 독립된 공간에서 실행할 수 있게 해주는 기술

* Docker Image

  * 응용프로그램을 실행 하는데 필요한 모든 것을 포함한 패키지
  * https://hub.docker.com/ 을 통해 버전관리
  * Dockerfile 덕분에 더이상 의존성 파일을 컴파일하고 설치할 필요가 없어짐

* Dockerfile

  * Docker Image를 구성하기 위해 있어야 할 패키지, 의존성, 소스코드등을 기록한 파일

  ![image-20230203170508753](C:\Users\YG\Desktop\Backend\프로젝트\약국\md-images\image-20230203170508753.png)

  * 주요 명령어

    * FROM : 이미지를 생성할 때 기반으로 사용할 이미지를 지정

      ex) FROM openjdk:11

    * ARG : 이미지 빌드 시점에서 사용할 변수 지정

      ex) ARG JAR_FILE=build/libs/app.jar

    * COPY : 호스트에 있는 파일이나 디렉토리를 Docker 이미지의 파일 시스템으로 복사

      ex) COPY ${JAR_FILE} ./app.jar

    * ENV : 컨테이너에서 사용할 환경 변수 지정

      ex) ENV TZ=Asia/Seoul

    * ENTRYPOINT : 컨테이너가 실행되었을 때 항상 실행되어야 하는 커맨드 지정

      ex) ENTRYPOINT [“java”, “-jar”, “./app.jar”]

* Docker Compose
  * 자동으로 멀티 컨테이너 라이프 사이클 관리
    * 서비스 구축
    * 네트워크 연결
    * 실행 순서
  * docker-compose.yml파일로 설정



# Docker 설치

1. https://docs.docker.com/desktop/ 각 OS에 맞게 Docker 설치

   * 재시작
   * WSL2 --needed 뜨면 cmd에 `wsl --update`
   * 설치 확인 : cmd에  `docker -v`

2. https://hub.docker.com/ 무료 플랜으로 회원 가입 및 로그인 인증

   * 회원가입
   * 로그인  `docker login`
     * id : 소문자로 입력  
     * pwd 

3. docker-copose 설치(Linux만)

   1. 설치

      ```sh
      $ sudo curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/
      local/bin/docker-compose
      ```

   2. 설치 확인

      ```sh
      $ docker -v
      $ docker-compose -v
      ```









# 싱글 Container 실습


## jar 파일 생성 

IntelliJ - terminal에서 진행

1. jar 파일 이름 설정

   * build.gradle에서 변경

     ```
     bootJar { archiveFileName = ‘app.jar’}
     ```

2. jar파일 생성

   ref) 삭제는 `gradlew clear`

   ```sh
   $ gradlew build (Window)
   $ ./gradlew build ( Linux, OSX)
   ```

3. jar 파일 확인

   경로 : build/libs/app.jar 

   

## Dockerfile 작성

1. Dockerfile 생성

   root project에 생성

2. 내용 작성

   * app

     ```
     FROM openjdk:11
     ARG JAR_FILE=build/libs/app.jar
     COPY ${JAR_FILE} ./app.jar
     ENV TZ=Asia/Seoul
     ENTRYPOINT ["java","-jar","./app.jar"]
     ```

   * db

     ```
     FROM mariadb:10
     ENV TZ=Asia/Seoul
     ```

   * redis

     ```
     FROM redis:6
     ENV TZ=Asia/Seoul
     ```

3. build

   ```sh
   docker build -t {dockerHub id}/{docker image 이름} .
   ```

4. docker image 확인

   ```sh
   docker images
   ```



## Docker Image 생성

IntelliJ - terminal에서 진행

1. build

   ```sh
   docker build -t {dockerHub id}/{docker image 이름} .
   ```

2. docker image 확인

   ```sh
   docker images
   ```

   

## Container 생성

1. Docker Image 실행

   ```sh
   docker run {dockerHub id}/{Docker Image 이름} -p {Host PORT}:{Container PORT}
   ```

2. 실행 확인 

   cmd에서 진행

   ```sh
   $ docker ps
   $ docker exec -it {CONTAINER ID} bash // 컨테이너를 bash 터미널 환경으로 접근
   $ docker stop [컨테이너 이름 또는 id]
   $ docker inspect [컨테이너 이름 또는 id]
   ```



# 멀티 Container 생성

## Dockerfile 작성

* app

  ```
  FROM openjdk:11
  ARG JAR_FILE=build/libs/app.jar
  COPY ${JAR_FILE} ./app.jar
  ENV TZ=Asia/Seoul
  ENTRYPOINT ["java","-jar","./app.jar"]
  ```

* db

  ```
  FROM mariadb:10
  ENV TZ=Asia/Seoul
  ```

* redis

  ```
  FROM redis:6
  ENV TZ=Asia/Seoul
  ```



## docker-compose.yml 작성

* version : 도커 컴포즈 버전 ( https://docs.docker.com/compose/compose-file/compose-versioning/)
* services : 이곳에 실행하려는 컨테이너들을 정의
  * 서비스 명
    * container_name : 컨테이너 이름
    * build : Dockerfile이 있는 위치
    * depends_on : 특정 컨테이너에 대한 의존관계
    * image: 실행할 Docker Image 이름
    * environment : 환경 변수
    * volumes : 호스트 디렉토리:컨테이너 디렉토리
    * ports : 접근 포트 설정(컨테이너 외부: 컨테이너 내부)
    * restart : 컨테이너 실행 실패하는 경우 재시작 여부



## docker mariaDB 설정

* 한글 깨짐 현상을 막기 위해 추가 설정
* docker-compose.yml에 경로 추가
  * docker mariaDB 컨테이너 내에 /etc/mysql/conf.d 경로에 .cnf 설정 파일을 넣어주면 해당 내용 바탕으로 설정이 초기화 됨



## profile 설정

application.yml에 설정 추가

* mariaDB
* Redis
* JPA

```yaml
spring:
	config:
 		activate:
 			on-profile: local
 	datasource:	
 		driver-class-name: org.mariadb.jdbc.Driver
 		url: jdbc:mariadb://localhost:3306/pharmacy-recommendation
 		username: ${SPRING_DATASOURCE_USERNAME}
 		password: ${SPRING_DATASOURCE_PASSWORD}
 	redis:
 		host: localhost
        port: 6379
 	jpa:
 		hibernate:
 			ddl-auto: create	# 개발 환경에서만 create
 		show-sql: true
```



## Docker Compose 환경변수 설정 방법

보안상 환경변수는 노출이 되면 안됨

* Compose 파일이 위치한 경로에 .env 파일을 구성 

  * 별다른 설정 없이 Docker Compose에 바로 반영
  * 실무에서는 보안을 위해 HashiCorp Vault를 사용

* 환경 변수 정보들 입력

  ```
  SPRING_DATASOURCE_USERNAME=root
  SPRING_DATASOURCE_PASSWORD=1234
  ```

* .gitignore에 .env 파일 추가







