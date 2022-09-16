# 1. Git

<img src="md-images/687474703a2f2f6e7669652e636f6d2f696d672f6769742d6d6f64656c4032782e706e67.png" alt="img" style="zoom: 33%;" />

* 사용처

  계획적인 릴리즈를 가지고 스케줄이 짜여진 대규모 프로젝트에 사용

- 과정

  feature → develop → release → hotfix → master

  

- branch 개념

  ### Master

  > 릴리즈 시 사용하는 최종 단계 메인 브랜치

  Tag를 통해 버전 관리를 한다.

  

  ### Develop

  > 다음 릴리즈 버전 개발을 진행하는 브랜치

  추가 기능 구현이 필요해지면, 해당 브랜치에서 다시 브랜치(Feature)를 내어 개발을 진행하고, 완료된 기능은 다시 Develop 브랜치로 Merge한다.

  

  ### Feature

  > Develop 브랜치에서 기능 구현을 할 때 만드는 브랜치

  한 기능 단위마다 Feature 브랜치를 생성하는게 원칙이다.

  

  ### Release

  > Develop에서 파생된 브랜치

  Master 브랜치로 현재 코드가 Merge 될 수 있는지 테스트하고, 이 과정에서 발생한 버그를 고치는 공간이다. 확인 결과 이상이 없다면, 해당 브랜치는 Master와 Merge한다.

  

  ### Hotfix

  > Mater브랜치의 버그를 수정하는 브랜치

  검수를 해도 릴리즈된 Master 브랜치에서 버그가 발견되는 경우가 존재한다. 이때 Hotfix 브랜치를 내어 버그 수정을 진행한다. 디버그가 완료되면 Master, Develop 브랜치에 Merge해주고 브랜치를 닫는다.

  

- Merge된 feature,release, hotfix는 닫아서 삭제

  



# 2. GitHub

![img](https://camo.githubusercontent.com/1206cff5e1da4b7ed742937a9234c6736c47db8866c3005ce0ae0266ad65c50f/68747470733a2f2f6d69726f2e6d656469756d2e636f6d2f6d61782f313136362f302a367054354834766e756a564c637930532e706e67)

* Git과 차의점

  * Git flow의 hot fix, feature -> GitHub pull request  => 일반 프로젝트에 사용

  * Master 브랜치에서 새로운 브랜치 추가해서 버그 해셜 or 기능 추가,  이 때 branch 이름 commit 메세지 주의해서 지을 것

  * Merge 전에는 `pull request`를 통해 공유하여 코드 리뷰 -> Master 브랜치에 Merge 요청

  * Merge가 완료되면, push를 진행하고 자동으로 배포가 완료된다. 



#### CI (Continuous Integration)

- 형상관리 항목에 대한 선정과 형상관리 구성 방식 결정
- 빌드/배포 자동화 방식
- 단위테스트/통합테스트 방식

> 이 세가지를 모두 고려한 자동화된 프로세스를 구성하는 것



# 3. GitLab

<img src="https://camo.githubusercontent.com/805cc97fd57cca10339b278f4db275f9a675dcb45cb76752d7bac5fc87b352fa/68747470733a2f2f61626f75742e6769746c61622e636f6d2f696d616765732f6769745f666c6f772f656e7669726f6e6d656e745f6272616e636865732e706e67" alt="img" style="zoom: 67%;" />

* GitHub과 차이점

  github-flow의 단점인 안정성과 배포 시기 조절 해결 by pre-production