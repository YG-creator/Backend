# Kubernetes 란?

Container 들을 관리하는 도구

* Docker 란?
  * 가상화 환경 vs 컨테이너 환경(Docker)
    * 가상화 환경 : 하이퍼바이저 > 가상머신 > 애플리케이션
    * 컨테이너 환경 : 운영체제 커널 > 애플리케이션 
      * 애플리케이션 더 많이 실행 가능
  * 초창기에는 필수였지만, 요즘은 패키지를 다운받아서 사용함
  * 배포 종류
    * 관리형 : 배포만 하면 자동 관리됨
    * 설치형 : package화 된거 설치
    * 구성형 : 자유롭게 구성, 교육용 -> kubeadm 사용할거(환경용의)



# 쿠버네티스 랩 환경 설치

1. 웹에서 제공하는 환경 - 제한적

   1. 플레이 쿠버네티스 

      시간제한, 직접 구성해야 됨, 새로고침시 다 날아감

   2. 쿠버네티스 플레이그라운드

      노드 제한적, 새로고침시 다 날아감

2. 코드로 설치 - 제한 없어서 선택

   VAGRANT(버추어박스에 코드 전달) + 버추어박스

   1. [버추어박스 설치](https://www.virtualbox.org/wiki/Downloads) > 앱 클릭해서 설치 확인
   2. [VAGRANT 설치](https://developer.hashicorp.com/vagrant/downloads?product_intent=vagrant) > terminal에서 `vagrant --version`으로 설치 확인
   3. [쿠버네티스 설치 코드 다운 ](https://github.com/sysnet4admin/_Lecture_k8s_starter.kit)> `C:\HashiCorp`에 압출 풀기 > terminal에서 쿠버네티스 설치 > 쿠버네티스 설치 코드로 이동(`cd /ch1/1.2/k8s-min-5GiB`) > 쿠버네티스 설치(vagrant up)

3. 쿠버네티스 랩을 쉽게 접근하기 위해 터미널 구성

   Supper Putty > Putty > 버추얼박스 노드

   1. [Supper PuttySetup 다운로드](https://github.com/jimradford/superputty/releases) > SupperPutty 설치 > 앱 실행 > putty.exe 경로 설정 (`C:\putty\putty.exe`) > 좌측 File-Import Sessions-From files 클릭 후 Sessions.XML 설정 > 
   2. [putty.exe 다운로드](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) > `C:\putty`에 putty.exe 파일 이동



# pod 배포하기

마스터 노드(kubectl) -> 워커 노드에 pod 단위로 애플리케이션 배포

pod : 컨테이너 집합

pod 배포

1. 마스터 노드에서 pod 생성 후 워크노드에 배포

   `kubectl run nginx --image=nginx`

2. pod  생성 확인

   `kubectl get pod`

3. pod IP 주소 확인

   `kubectl get pod -o wide`

4. nginx 확인

   `curl IP주소` > Thank you for using nginx 문구 나옴

   



# pod service expose 

1. 배포한 pod가 외부에서 접근 안됨

   cmd에서 `curl 172.16.132.1` 하면 결과물이 안나옴

2. nginx pod service(NodePort) 열어주기

   `kubectl expose pod nginx --type=NodePort --port=80`

3. service IP 확인 

   `kubectl get service` 

4. 노드 IP 확인

   `kubectl get nodes -o wide`

5. 외부에서 노드 IP : service IP 입력하면 접속 성공함

   cmd에서 `curl 192.168.1.101:32101`

   



# Deployment 배포하기

pod 여러개 배포하기

1. 생성 및 배포

   * kubectl run : pod 

   * kubectl create : pod + deploy

     `kubectl create deployment 이름 --image=nginx`

   * kubectl apply : pod + deploy

2. 확인

   1. deployment 생성 확인 > IP 확인

      `kubectl get podes -o wide`

   2. nginx 접속 확인

      `curl IP주소`

3. 배포 수 늘리기

   `kubectl scale deployment 이름 --replicas=갯수`



# deploy service expose

1. NodePort로 노출은 가능하나 노드 IP를 알려줘야 됨

   `kubectl expose deployment 이름 --type=NodePort --port=80`

2. 노드포트보다 로드밸런서가 좋은 점

   1. 노드 IP 노출이 안됨

   2. 가야할 경로를 최적화 가능 

      Externerl IP 바로 알 수 있음

3. 로드밸런서 사용해보기

   1. MetalLib 설치

      `kubectl apply -f MetalLib파일경로`

   2. deployment 생성

      ` kubectl create deployment 이름 --image=sysnet4admin/이름`

   3. deployment  갯수 늘리기

      `kubectl scale deployment 이름 --replicas=3`

   4. deployment  생성 확인

      `kubectl get pods`

   5. deployment  외부로 노출하기

      `kubectl expose deployment 이름 --type=LoadBalancer --port=80`

   6. Externerl IP 확인

      `kubectl get service`



# 정리

1. pod : container 집합

2. deployment : pod 여러개 

3. service : 노드에 가기 위해서 거쳐가야 하는 곳 / NodePort, LoadBalancer



# 삭제

kubectl delete 유형 이름

1. service

   `kubectl delete service 이름`

2. deploy

   `kubectl delete deploy 이름`

3. pod

   `kubectl delete pod 이름`

4. MetalLib

   `kubectl delete -f 경로`



# 쿠버네티스 구성 요소

1. 구성요소 확인

   kube-system 네임스페이스 아래에 있음

   `kuebectl get pods -n kube-system`

2. 기본 철학

   * MSA

   * 선언적인 시스템 : 추구하는 상태와 현재 상태를 맞추려고 함(감시 > 차이발견 > 상태변경)

     ![image-20230609164103955](md-images/image-20230609164103955.png)

3. 현재 쿠버네티스의 pod 배포 흐름

   ![image-20230609163813552](md-images/image-20230609163813552.png)



# 문제 발생

1. pod vs deployment 에서 pod 삭제

   * pod는 사라짐

   * deployment에서 pod 삭제 시 자동 생성됨

2. 워커 노드의 구성요소에 문제가 발생하면?

   * kubelet : 문제가 발생하면 복구가 안되서 배포안됨

   * 컨테이너 런타임 : 5분 뒤에 복구가 되서 정상 배포가능, 그전까지는 정상배포 안됨

   * 스케줄러 역할 : 최대한 균등하게 pod를 워커노드에 배포함

3. 마스터 노드의 구성요소에 문제가 발생하면?

   * 스케줄러 : 문제가 발생하면 삭제하고 복구함

   * kuberlet : 문제가 발생해도 정상 작동함

   * 컨테이너 런타임 : 복구는 안됨, 일시 중지됨

     

# 쿠버네티스 오브젝트

1. 기본 오브젝트(추구하는 상태 기술) 확인 

   * 추구하는 상태(Spec), 현재 상태(Status) 확인

   * 오브젝트 변경해보기

     `kubectl edit deployment 이름`  > spec:replicas 값 변경해보기 > `kubectl get pods`로 확인하기

2. 기본 오브젝트

   1. pod

   2. 서비스 - NodePort, LoadBalancer

   3. 네임 스페이스 - default, kube-system

   4. 볼륨 - 영속적인 데이터 보존 

      ex) 접속 기록 유지 됨



# kubectl 팁

1. kubectl 자동완성 : 가능한 명령어들 목록 출력됨

   * vagrant가 bash에 자동 추가함

   * <tab><tab> 으로 가능

2. 배시 셀에 별명 지어주기(alias)

   k=kubectl

3. 버전 업그레이드

   랩환경, 현업은 다름

   게획 > kubeadm 업그레이드 > kubelet 업그레이드 > 업그레이드 확인

   1. 현재 버전 확인

      `k get nodes`

   2. 계획

      `kubeadm upgrade plan`

   3. kubeadm 업그레이드

      `yum upgrade kubeadm-버전 -y`

   4. kubeadm 업그레이드 확인

      `kubeadm --version`

   5. 업그레이드 계획 적용

      `kubeadm upgrade apply 버전 -y`

   6. kubeadm-config를 사용해서 내부 configuration을 바꾼경우 아래과정 진행(선택)

      ![image-20230609180335221](md-images/image-20230609180335221.png)

   7. kubelet 업그레이드
      `yum upgrade kubelet-버전 -y`

   8.  kubelet 업그레이드 확인

      `kubelet --version`

   9. 모든 노드에서 kubelet  restart

      `systemctl restart kubelet`

   10. 모든 워커 노드에서 reload

       `systemctl daemon-reload`

   11. 버전 확인

       `k get nodes`

4. 오브젝트 예약 단축어

   1. no : Node
   2. ns : Namespace
   3. deploy : Deployment
   4. po : Pod
   5. svc : Service




# DNS  정보 조회

공식문서 확인





