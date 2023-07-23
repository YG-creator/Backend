# 1. Component

## 1.1. Cluster Architecture

고가용성, 확장성, 자동 복구 기능 제공해서 사용

- Kubernetes는 Master Node와 Worker Node로 구성되어 있음
  - Master Node 
  
    클러스터 제어 중심 노드
  
    - ETCD : 클러스터의 구성 정보 저장
    - Kube-API Server : 클러스터에 대한 모든 조작 가능하게 하는 RESTful API 제공
    - Kube Controller Manager : Container를 관리하는 Controller(Pod, ReplicaSet, Deployment, 상태) 관리자
    - Kube Scheduler : Pod를 어느 노드에 배치할지 스케줄링
  
  - Worker Node 구성
    
    컨테이너가 실행하는 노드
    
    - Kubelet : 마스터의 명령에 따라 노드 상태 보고, 파드 실행 및 관리 : 장애 모니터링과 복구 기능
    - Kube Proxy : 클러스터 내 모든 포드 연결
    - 컨테이너 런타임 : 컨테이너 실행하고 관리



## 1.2. ETCD

- 역할

  - 클러스터에 관한 정보(Nodes, Pods, Configs, Secrets, Accounts, Roles, Bindings) 저장

- 특징

  - 분산되고 신뢰할 수 있는 key-value Store
    - 각 개인당 문서 하나를 갖고 개인에 대한 모든 정보가 해당 key-value형식으로 파일에 저장 됨 
    - 한 파일의 변화가 다른 파일에 영향을 안줌

- 설치 방법

  - 직접 다운로드해서 설치

    `wget -q --https-only \``"https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"`

  - kubeadm 사용하여 설치

    `kubectl exec etc-master -n kube-system`

- 명령어

  | 명령어                                           | 기능                            |
  | :----------------------------------------------- | :------------------------------ |
  | `./etcd`                                         | 실행                            |
  | `./etcdctl set key1 value1`                      | key-value 저장                  |
  | `./etcdctl get key1`                             | key 값 조회                     |
  | `./etcdctl`                                      | 명령어 조회                     |
  | `./etcdctl --version`                            | 버전 확인(etcdctl, API version) |
  | `export ETCDTL_API={버전숫자} ./etcdctl version` | etcdctl version 변경하기        |



## 1.3. Kube-API Server

- 역할

  - User 인증
  - Request 유효성 확인
  - ETCD와 상호작용(조회, 변경)
  - 스케줄려와 상호작용
  - kubelet에 실행 명령

- 설치 방법

  - 직접 다운로드해서 설치

    `wget https:``//storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver`

- 옵션 보기

  - kubeadm 사용해서 옵션 보기

    `cat /etc/kubernetes/manifests/kube-apiserver.yaml`

- - kubeadm 안사용하고 옵션 보기

    1. api-server 옵션 보기

       `cat /etc/systemd/system/kube-apiserver.service`

    2. 프로세스 실행과 효과적인 옵션도 볼 수 있음

       

       

       `ps -aux | grep kube-apiserver`

       

       

## 1.4. Kube Controller Manager

- 역할

  - container 를 관리하는 controller 관리자
    - 상태 모니터링
    - 상태 변경

- 옵션 보기

  - kubeadm 사용해서 옵션 보기

    `cat /etc/kubernetes/mainifests/kube-controller-manager.yaml`

  - kubeadm 안사용하고 옵션 보기

    1. Kube Controller Manager 옵션 보기

       `cat /etc/systemd/system/kube-controller-mangager.sesrvice` 

    2. 프로세스 실행과 효과적인 옵션도 볼 수 있음

       `ps -aux | grep kube-controller-mangager` 

## 1.5. Kube Scheduler

- 역할

  - 어떤 POD가 어떤 Node에 들어갈지만 결정
    - 조건에 안맞는 node 필터링
    - 우선순위 따지기

- 설치 방법

  - 직접 다운로드해서 설치

    `wget https:``//storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler`

- 옵션 보기

  - kubeadm으로 옵션 보기

    `cat /etc/kubernetes/mainifests/kube-scheduler.yaml`

  - kubeadm 안사용하고 옵션 보기

    - Kube-Scheduler 옵션 보기

      `cat /etc/systemd/system/kube-scheduler.sesrvice`

    - 프로세스 실행과 효과적인 옵션도 볼 수 있음

      `ps -aux | grep kube-scheduler`

## 1.6. Kubelet

- 역할

  - 클러스터로 노드를 등록

  - POD를 생성

    Kube-scheduler에 의해

  - Node와 POD를 모니터링

- 설치 방법

  - 직접 다운로드해서 설치

    `wget https:``//storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet`

- 옵션 보기

  - kubeadm 안사용하고 옵션 보기

    `ps -aux | grep kubelet`

## 1.7. Kube Proxy

- 역할

  - 클러스터 내 모든 포드 연결

- 설치 방법

  - 직접 다운로드해서 설치

    `wget https:``//storage.googleapis.com/kubernetes-release/v1.13.0/bin/linux/amd64/kube-proxy`

- 확인하기

  - kubeadm으로 Kube Proxy 확인하기

    `kubectl get daemonset -n kube-system`



# 2. Core Concept

## 2.1. Pod

- 역할
  - 앱의 단일 인스턴스



- 생성 방법

  - 설정파일(yaml) 작성 후 생성하기

    1. 설정파일(yaml) 작성

       `vi {파일이름}.yaml`

       `apiVersion: v1``kind: Pod``meetadata:``  ``name: {Pod이름}``  ``labels:``    ``app: {앱이름}``    ``type: {앱타입}` `spec:``  ``containers:``    ``- name: {컨테이너이름}``    ``image: {이미지이름}``:wq!`

    2. 설정파일을 토대로 Pod 생성

       `kubectl create -f {파일이름}.yaml`

  - 명령어로 간단하게 생성 및 설정하기

    - 바로 Pod 생성하기

      `kubectl run {Pod이름} --image={image이름}`

    - 설정파일 작성 후 Pod 생성

      `kubectl run nginx --image=nginx --dry-run=client -o yaml > {파일이름}.yaml``kubectl create -f {파일이름}.yaml`

- 조회 방법

  존재하는 pod, pod 상태, pod이름 확인 가능

  `kubectl get pods`

- 상세 조회 방법

  어떤 image로 생성 됐는지, 컨테이너 상태, 에러 원인 확인 가능

  `kubectl describe pod {pod이름}`

- 삭제 방법

  `kubectl delete pod {pod이름}`

- 수정 방법

  1. 설정 파일 수정하기

     `vi {파일이름}.yaml`

  2. 수정사항 적용하기

     `kubectl apply -f {파일이름}.yaml`

  3. 적용 됐는지 확인하기

     `kubectl get pods`

## 2.2. ReplicaSet

- 역할

  - 고가용성 : 응용어플리케이션이 항상 실행되도록 하기 위해 포드를 복사함
  - 로드 밸런싱 : 서로 다른 노드의 여러 포드에 걸쳐 부하 분산

- 생성 방법

  1. 설정 파일 작성

     `vi {파일이름}.yaml`

     `apiVersion: apps/v1``kind: ReplicaSet``metadata: ` `spec:`` ``template:``  ``{apiVersion,kind를 제외한 pod 설정 파일 내용}``  ``replicas: {복제갯수}``  ``selector:``    ``{Pod metadata 하위 key-value 넣기}`

  2. 설정 파일 토대로 Replica Set 생성

     `kubectl create -f {파일이름}.yaml`

- 조회 방법

  `kubectl get rs`

- 상세 조회 방법

  `kubectl describe rs {ReplicaSet이름}`

- 삭제 방법

  `kubectl delete rs {ReplicaSet이름}`

- 수정 방법

  1. 설정 파일 수정

     `kubectl edit rs {rs이름}` `OR` `vi {파일이름}.yaml`

  2. 수정 적용하기

     `kubectl replace -f {파일이름}.yaml`

  3. 확인하기

     `kubectl get rs`

  4. 생성이 안됐으면 기존 pod 삭제

     `kubectl delete pod {pod이름}`

## 2.3. Deployment

- 역할

  애플리케이션의 배포와 관리(버전 업데이트, 롤백, 스케일링)를 담당

- 생성 방법

  - yaml 파일 작성 후 Deployment 생성

    1.  설정파일 작성

       `apiVersion: apps/v1``kind: Deployment``metadata: ` `spec:`` ``template:``  ``{apiVersion,kind를 제외한 pod 설정 파일 내용}``  ``replicas: {복제갯수}``  ``selector:``    ``{Pod metadata 하위 key-value 넣기}`

    2. 설정 파일 토대로 Deployment 생성

       `kubectl create -f {파일이름}.yaml`

  - 명령어로 간단히 생성

    - 바로 생성하기

      `kubectl create deployment --image=nginx nginx`

    - yaml 파일 작성 후 생성하기

      `kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml``kubectl create -f nginx-deployment.yaml`

## 2.4. Service

- 역할

  - 애플리케이션 안팎의 다양한 구성 요소간의 통신을 가능하게 함

- 종류

  - NodePort
    - 역할
      - 노드의 포트와 내부 포트 매핑 + 내부 포트와 Pod IP 매핑
      - ssh로 Node 접속한 뒤 내부 Pod로 접속할 필요 없이 바로 Node에 접속하면 애플리케이션 실행 됨
    - 생성 방법
  - ClusterIP
    - 역할
      - ssh로 Node 접속한 뒤 내부 Pod로 접속할 필요 없이 바로 Node에 접속하면 애플리케이션 실행 됨
      - 중간다리 역할을 해서 Pod를 변경해도 서비스 간 통신에 영향을 안 미치게 됨
    - 생성 방법
  - LoadBalancer
    - 역할
      - 사용자에게 단일 IP를 제공 + 알아서 부하 분산
    - 생성 방법

- 조회 방법

  `kubectl get services`

- 상세 조회 방법

  `kubectl describe service {service이름}`

- 수정 방법

  1. yaml 파일 수정
  2. 수정 적용

- 삭제 방법

  `kubectl delete service {service이름}`



## 2.5. Namespace

- 역할
  - 오브젝트를 논리적으로 구분하여 그룹핑 함
- 생성 방법
- 옵션
- Resource Quota
- 조회 방법
- 상세 조회 방법