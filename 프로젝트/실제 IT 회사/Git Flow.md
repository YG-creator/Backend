# Git Flow

## 개념

Git을 사용하는 환경에서

Branch간의 문제없이 배포까지 안정적으로 할 수 있도록 

Branch를 관리하는 전략



## Branch

1. Main(Master)

   실제 운영 환경에 있는 코드

2. Dev

   다음 release에 담을 코드

3. Feature

   새로운 개발 코드

4. Release

   배포할 코드

5. Hotfix

   의도치 않은 에러 수정 코드



## 사용법

1. Release Branch 생성 후 추가 작업이 필요해질 경우
   1. main에 dev branch 생성
   2. dev의 feature branch 작업
   3. dev에 feature merge
   4. conflict 해결
   5. dev에 release branch 생성
   6. release에 feature branch 작업
   7. release에 feature merge
   8. main에 release merge
   9. dev에 main merge : 최신이 아닌 코드에 작업하는거 방지
2. Release Branch 생성 후 추가 작업이 없는 경우
   1. main에 dev branch 생성
   2. dev의 feature branch 작업
   3. dev에 feature merge
   4. dev에 release branch 생성
   5. main에 release merge
   6. dev에 main merge : 최신이 아닌 코드에 작업하는거 방지
3. Hotfix가 나가야 할 경우
   1. main에 hotfix branch 생성
   2. hotfix에 작업
   3. main에 hotfix merge
   4. dev에 main merge : 최신이 아닌 코드에 작업하는거 방지
   5. 다른 개발자들은 feature에 dev merge하고 작업하기