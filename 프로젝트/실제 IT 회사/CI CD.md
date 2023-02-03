# CI/CD란?

* CI(Continuous Integration)

  자동으로 프로젝트에 머지된다.

* CD(Continuous Deployment)

  자동으로 코드가 배포된다

* 필요한 이유

  개발 편의성, 효율성



# AWS

## EC2 설정

Compute

1. free tier로 AWS 회원가입(1년 무료)

2. Compute - EC2 클릭

   1. Launch Instance 클릭

      1. 오른쪽 상단 지역 선택(Seoul)

      2. OS 선택(ubuntu)

      3. Server 선택(Ubuntu Server 20.04)

      4. Instance type 선택(t2.micre)

      5. Create key pair(이름 설정, RSA,.pom(처음만 발급 가능))

         key로 최초 로그인 후에 나중에 비밀번호로도 할 수 있도록 바꿀 예정

      6. Launch Instance 클릭

   2. View all Instances 클릭

   3. tag 값 설정

      CD를 위해서 key 설정

      Instance 다 생기면 오른쪽클릭 -> Add/Edit Tags -> Create Tag -> key 입력 -> Save

   4. IP 보안을 위해 In bound 설정

      1. 하단 Security groups -> launch wizard-4 클릭
      2. 하단 Inbound rules 클릭
      3. Edit inbound rules 클릭
      4. Add rule 클릭
      5. 설정
      6. save rules

   5. 정적인 IP 발급(서버 종료시 IP도 같이 삭제 해줘야 됨)

      1. 왼쪽 Network & Security -> Elastic IPs
      2. 우측 상단 Allocate Elastic IP address 클릭
      3. allocate
      4. 성성한 IP 클릭
      5. Associate Elastic IP address 클릭
      6. Instance 선택
      7. Associate
      8. Instance에서 보면 Elastic IP가 할당 되어있음

   6. Intance 연결하기

      1. Comment 클릭

      2. example 첫번째 줄 복사

      3. cmd 실행

      4. key 있는 곳으로 이동

         cd Downloads

      5. key 확인

         ls

      6. server 연결

         1. sudo + 붙여넣기

         2. yes

      7. ubuntu로 접속됨

   7. update 하기

      `sudo apt update && sudo apt upgrade`

   8. java 설치

      1. `sudo apt install openjdk-11-jdk `

      2. `Y`

      3. 설치 확인 

         `java-version`

   9. ruby 설치

      `sudo apt install ruby-full`

   10. wget 설치

       `sudo apt install wget`

   11. ubunto server로 이동 

       `cd /home/ubuntu`

   12. CodeDeploy 에이전트 설치

       1. `wget https://{bucket-name}.s3.{region-identifier}.amazonaws.com/latest/install`
          * bucket-name과 region-identifier는 AWS CodeDeploy 참조해서 작성

       2. `chmod +x ./install`
       3. `sudo ./install auto > /tmp/logfile`

   13. 서비스가 실행 중인지 확인

       1. `sudo service codedeploy-agent status`
       2. active면 통과

   14. Instance에 역할 부여(IAM)

       1. 왼쪽상단 Services 클릭 -> IAM 클릭
       2. 왼쪽 Roles 클릭
       3. create role
       4. 설정(AWS service, EC2)
       5. next 클릭
       6. 검색기능 -> AWSCodeDeployFullAccess 체크, S3Full 체크 -> next
       7. Role-name설정(IAM-EC2)
       8. create role
       9. 왼쪽상단 Services 클릭 -> EC2 클릭 -> Instances 클릭
       10. Instance에 연결하기
           1. 해당 instance 오른쪽 클릭 
           2. Instance Settings 클릭
           3. Attach/Replace IAM Role 클릭
           4. IAM role 선택 -> apply

   15. key 없이도 접속 할 수 있도록 하기

       1. ubunt 나오기

          `exit`

       2. `sudo ssh -i "EC2-KEY-PAIR.pom" utuntu@ec2-3-36-252-36.api-northeast-2.com`pute.amazonaws.com

       3. 컴퓨터 비밀번호 입력

       4. `sudo vi /etc/ssh/sshd_config`

       5. Passord 부분 yes 로 변경

       6. wq 로 나오기

       7. ubuntu에 권한 주기

          `sudo su -`

       8. 비밀번호 변경

          `passwd ubuntu`

          `새 비밀번호 입력`

       9. 나가기

          `exit`

       10. key 없이 접속해보기

       1. 서비스 재시작

          `sudo service ssh restart`

       2. 나가기

          `exit`

       3. 서비스에 접속

          `ssh utuntu@ec2-3-36-252-36.api-northeast-2.compute.amazonaws.com`

       4. 비밀번호 입력

       

   

## RDS 설정

Database

1. Database - RDS -> Dashboard
2. create database
3. 설정(공부용)
   1. easy create
   2. MySQL
   3. free tier
   4. Identifier 설정(fastcampus-rds)
   5. Master username 설정(admin)
   6. 비밀번호 설정
   7. default 세팅 건들지 않고 확인(port 번호, db version)
   8. create database
4. 다 생성 되면 Endpoint, Port, master name, pwd 확인





## S3 설정

Storage

1. Stroage - S3 클릭
2. region은 바뀌어도 상관없음
3. create bucket 클릭
4. 설정
   1. Bucket name(unique 해야됨)
   2. Block all public access 체크 : access에 돈이 들으므로 차단
   3. create bucket
5. IAM 생성
   1. 왼쪽위 Services - IAM 클릭
   2. 왼쪽 Users 클릭
   3. Add User
   4. User name 입력
   5. Access key 체크
   6. next
   7. Attach existing policies 클릭
   8. F3 full, AWSCodeDeployFullAccess 체크
   9. next
   10. next
   11. create user
   12. User, Access key , Secret access key 따로 저장해두기 -> GitHub에서 사용할거
   13. close

6. GitHub에 User IAM 할당

   1. repository 생성
   2. Settings -- Security - Actions에서 secret 변수 생성
      1. AWS_ACCESS_KEY_ID 생성
         1. New repository secret 클릭
         2. Name : AWS_ACCESS_KEY_ID
         3. Secret : Access key 복붙 
      2. AWS_SECRET_ACCESS_KEY 생성
         1. New repository secret 클릭
         2. Name : AWS_SECRET_ACCESS_KEY
         3. Secret : Secret access key 복붙 
      3. AWS_REGION 생성
         1. New repository secret 클릭
         2. Name : AWS_REGION 
         3. Secret : ap-northeast-2

   

## Code Deploy 설정

코드 자동 배포

1. Developer Tools -> CodeDeploy 클릭
2. IAM 생성
   1. 왼쪽 상단 Services - IAM 클릭
   2. 왼쪽상단 Roles 클릭
   3. Create role 클릭
   4. step1
      1. Use cases for other AWS services에 CodeDeploy 입력
      2. 가장 위 CodeDeploy 체크
      3. next
   5. step2
      1. next
   6. step3
      1. Role name 입력
      2. create role
3. CodeDeploy application 생성
   1. 왼쪽 상단 Services - CodeDeploy 클릭
   2. 왼쪽 Applications 클릭
   3. application 생성
      1. Create application 클릭
      2. Applicaiont name 설정
      3. Compute platform을 EC2 로 선택
      4. create applicaiont 선택
   4. deployment group 생성
      1. create deployment group
      2. group name 입력
      3. service role - 방금만든 IAM 선택
      4. Environment configuration - Amazon EX2 instances 체크
      5. key  - mapping된 key 선택
      6. create deployment group 클릭



# Git Action

자동으로 코드 배포하기 설정

## deploy.yaml 작성

1. GitHub repository 열기

2. Add file - Create new file 클릭

3. 경로 작성 

   reposiory이름/.github/workflows/deploy.yaml 되도록 경로 입력

4. script 작성

   ```yaml
   name: CI-CD
   
   # 어떤 branch push 했을 때 작동할 것인지 설정
   on:
     push:
       branches:
         - main
   
   # 변수 설정, 값들은 AWS 참조
   env:
     S3_BUCKET_NAME: s3-fastcampus
     RESOURCE_PATH: ./src/main/resources/application.yaml
     CODE_DEPLOY_APPLICATION_NAME: CODE-DEPLOY-FAST-CAMPUS
     CODE_DEPLOY_DEPLOYMENT_GROUP_NAME: CODE-DEPLOY-GROUP
   
   # 작업들
   jobs:
     build:	# 빌드
       runs-on: ubuntu-latest	#작업 환경,EC2 server와 같으면 좋음
       
       steps:	# 수행할 작업들
         - name: Checkout
           uses: actions/checkout@v2	
   		
         - name: Set up JDK 11	
           uses: actions/setup-java@v1	#설치
           with:
             java-version: 11	## java11 사용
   
         - name: Set yaml file
           uses: microsoft/variable-substitution@v1
           with:
             files: ${{ env.RESOURCE_PATH }}
           env:
             override.value: ${{ secrets.DI_FROM_SECRET }}
   
         - name: Grant execute permission for gradlew
           run: chmod +x ./gradlew	# gradelw 권한 주기
           shell: bash
   
         - name: Build with Gradle
           run: ./gradlew build	# gradlew로 빌드하기, multimodule을 사용하거나 추가옵션 추가필요 시 수정
           shell: bash
   
         - name: Make zip file
           run: zip -r ./$GITHUB_SHA .	# 빌드된 결과를 zip 파일로 올리기
           shell: bash
   
         - name: Configure AWS credentials
           uses: aws-actions/configure-aws-credentials@v1	# AWS 권한 접근 권한 인증
           with:
             aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}	# ${{ secrets.GitHub secret 변수이름}}
             aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
             aws-region: ${{ secrets.AWS_REGION }}
   
         - name: Upload to S3
           run: aws s3 cp --region ap-northeast-2 ./$GITHUB_SHA.zip s3://$S3_BUCKET_NAME/$GITHUB_SHA.zip # zip파일 AWS S3에 올리기
   
         - name: Code Deploy	
           run: |	
             aws deploy create-deployment \	# code deploy하기
             --deployment-config-name CodeDeployDefault.AllAtOnce \	
             --application-name ${{ env.CODE_DEPLOY_APPLICATION_NAME }} \ 	# application 이름 입력
             --deployment-group-name ${{ env.CODE_DEPLOY_DEPLOYMENT_GROUP_NAME }} \	# groupo 이름 입력
             --s3-location bucket=$S3_BUCKET_NAME,bundleType=zip,key=$GITHUB_SHA.zip	# bucket이름과 타입 입력
   
   ```

   

## appspec.yaml 작성

애플리케이션 사양파일

1. local에 repository clone
2. IntelliJ에서 신규프로젝트 생성
   1. JDK11, Java11
   2. dependeny - lobok, spring web

3. root project에 appspec.yml 생성 후 작성

   ```yaml
   version: 0.0
   os: linux
   files:
     - source: /
       destination: /home/ubuntu/github_action
       overwrite: yes
   
   permissions:
     - object: /
       pattern: "**"
       owner: ubuntu
       group: ubuntu
   
   hooks:
     ApplicationStart:
       - location: scripts/gh_deploy.sh
         timeout: 60
         runas: ubuntu
   ```

4. AWS code-deploy 재실행

   1. cmd 실행
   2. `sudo service codedeploy-agent restart`

5. log로 Code deploy 확인

   `tail -F /var/log/aws/codedeploy-agent/codedeploy-agent.log`

6. remote로 push 



## script 작성

application 동작법, 로그 설정, 백그라운드에서도 동작하기, multimodule, port 설정 등

1. script 파일 생성하기

   1. 루트 프로젝트에 scripts 폴더 생성

   2. gh_deploy.sh 파일 생성

      ```sh
      #!/bin/bash
      # 변수 설정
      PROJECT_NAME="github_action"
      JAR_PATH="/home/ubuntu/github_action/build/libs/*.jar"
      DEPLOY_PATH=/home/ubuntu/$PROJECT_NAME/
      DEPLOY_LOG_PATH="/home/ubuntu/$PROJECT_NAME/deploy.log"
      DEPLOY_ERR_LOG_PATH="/home/ubuntu/$PROJECT_NAME/deploy_err.log"
      APPLICATION_LOG_PATH="/home/ubuntu/$PROJECT_NAME/application.log"
      BUILD_JAR=$(ls $JAR_PATH)
      JAR_NAME=$(basename $BUILD_JAR)
      
      # 로그 남기기
      echo "===== 배포 시작 : $(date +%c) =====" >> $DEPLOY_LOG_PATH
      
      echo "> build 파일명: $JAR_NAME" >> $DEPLOY_LOG_PATH
      echo "> build 파일 복사" >> $DEPLOY_LOG_PATH
      cp $BUILD_JAR $DEPLOY_PATH
      
      echo "> 현재 동작중인 어플리케이션 pid 체크" >> $DEPLOY_LOG_PATH
      CURRENT_PID=$(pgrep -f $JAR_NAME)
      
      # 동작중인 application을 죽이기 + 로그남기기
      if [ -z $CURRENT_PID ]
      then
        echo "> 현재 동작중인 어플리케이션 존재 X" >> $DEPLOY_LOG_PATH
      else
        echo "> 현재 동작중인 어플리케이션 존재 O" >> $DEPLOY_LOG_PATH
        echo "> 현재 동작중인 어플리케이션 강제 종료 진행" >> $DEPLOY_LOG_PATH
        echo "> kill -9 $CURRENT_PID" >> $DEPLOY_LOG_PATH
        kill -9 $CURRENT_PID
      fi
      
      # 새롭게 application 띄우기 
      DEPLOY_JAR=$DEPLOY_PATH$JAR_NAME
      echo "> DEPLOY_JAR 배포" >> $DEPLOY_LOG_PATH
      nohup java -jar $DEPLOY_JAR >> $APPLICATION_LOG_PATH 2> $DEPLOY_ERR_LOG_PATH &	# 백그라운드에서도 동작,multimodule property, 																				# server port 설정도 가능
      
      sleep 3
      
      echo "> 배포 종료 : $(date +%c)" >> $DEPLOY_LOG_PATH
      ```

2. remote로 push 하기



## 중요정보 노출안하기

* 상황

  * 중요한 정보 노출됨
  * gitignore 처리해서 위문제를 처리하면 빌드가 안됨

* 해결방안

  * 빌드시 동적으로 변수값 주입하기

    1. application.yaml 파일 gitignore 설정

       `./src/resources/application.yaml`

    2. github - secret에 중요정보 입력

       Settings - Security - Actions - add new secret클릭 -> name, secret입력 add secret

    3. deploy.yaml 파일 설정

       1. 경로 변수(env) 추가

          ```yaml
          env:
            RESOURCE_PATH: ./src/main/resources/application.yaml
          ```

       2. secret값 주입하기

          ```yaml
          - 	name: Set yaml file
          	uses: microsoft/variable-substitution@v1	# 값 덮어쓰기
          	with:
          		files: ${{ env.RESOURCE_PATH }}			# 파일 경로
          	env:
          		override.value: ${{ secrets.DI_FROM_SECRET }}	# secret값 주입
          ```



# 확인

1. GitHub - Actionos에서 build 확인

2. S3 확인 

   AWS-S3 - zip 파일이 올라감

3. CodeDeploy 확인

   1. AWS CodeDeploy 확인

   2. deploy log 보기

      `cat deploy.log`

   3. deploy error log 보기

      `cat deploy_err.log`

4. server 확인 by cmd

   1. `lsof -i:8080`
   2. `ps -ef | grep java`

5. 이제 push만 하면 자동 배포가 됨
