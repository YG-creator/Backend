# Cluster 구조

1. 쿠버네츠 목적

   응용 프로그램을 자동화된 방식으로 컨테이너 형식으로 호스트하는 것

   요구에 따라 응용 프로그램의 많은 인스턴스를 쉽게 배포하 가능하고 응용 프로그램 내 다양한 서비스 간의 통신이 쉽게 가능해짐

2. Kubernetes 구조

   1. 마스터노드

      클러스터를 관리하고 서로 다른 노드에 대한 정보를 저장하고 어떤 컨테이너가 어디로 갈지 계획하고 노드와 컨테이너를 모니터링하는 책임지

      1. ETCD CLUSTER

         클러스터에 관한 정보 저장()

      2. Kube-scheduler

         노드의 응용프로그램이나 컨테이너의 스케줄을 짜는 역할

      3. Controller Manager

         1. Node

            노드 

         2. Replication

            복제

      4. Kube-apiserver

         모든 작업을 지휘

   2. 워커노드

      컨테이너를 로딩할 수 있는 배에 비유

      1. kubelet

         Kube-apiserver 서버의 지시를 듣고 컨테이너와 kube-porxy를 관리

      2. kube-porxy

         워커 노드간의 통신

      3. Container Runtime Engine(rkt)

         컨테이너 실행 및 관리



# Docker vs ContainterD

1. 차이점

   * Docker 
     * Docker는 종합적인 컨테이너 플랫폼으로 다양한 기능과 툴을 제공하는 컨테이너 런타임 엔진
     * Docker는 큰 커뮤니티와 생태계를 가지고 있으며 Docker 이미지와 Dockerfile을 사용
   * ContainerD
     * containerd는 경량화되고 모듈식인 컨테이너 런타임 엔진
     * containerd는 CNCF의 프로젝트로써 보다 단순하고 표준화된 컨테이너 런타임 엔진을 제공하며, OCI와 호환됨

2. Interface 

   |         | 목적      | 커뮤니티   | 사용하는 곳                 |
   | ------- | --------- | ---------- | --------------------------- |
   | car     | 디버깅    | ContainerD | ContainerD                  |
   | nexdctl | 일반 목적 | ContainerD | ContainerD                  |
   | crictl  | 디버깅    | Kubernetes | All CRI Compatible Runtimes |

   기존에는 노드에 도커가 설치가 의무적이었지만 car 이나 nexdctl를 사용했지만, 이제는 설치할 필요가 없으니 crictl을 사용하면 됨

   

# ETCD 기본

1. ETCD 란?

   분산되고 신뢰할 수 있는 key-value Store로, 간단하고 안전하며 신속하다.

2. Key-value Store이란?

   * DB의 경우, 열을 추가하면 전체 테이블이 영향을 받아서 빈방이 많이 생김

   * 각 개인당 문서 하나를 갖고 개인에 대한 모든 정보가 해당 key-value형식으로 파일에 저장 됨
   * 한 파일의 변화가 다른 파일에 영향을 안줌
   * 복잡해지면 JSON 이나 YANO 데이터 포맷을 사용함

3. ETCD 설치

   1. 바이너리 다운로드

      ```
      curl -L https://github.com/etcd-io/etcd/releases/download/v3.3.11/etcd- v3.3.11-linux-amd64.tar.gz -o etcd-v3.3.11-linux-amd64.tar.gz
      ```

   2. 추출

      `tar xavf etc-v3.3.11-linux-amd64.tar.gz`

   3. 실행

      `./etcd`

4. ETCD 사용법

   1. 컨트롤 클라이언트 

      1. 실행

         `./etcd`

      2. 명령어 작성

         * 저장

           `./etcdctl set key1 value1`

         * 조회

           `./etcdctl get key1`

         * 명령어 조회

           `./etcdctl `

      3. Version 관리

         * 버전 확인

           `./etcdctl --version`

           * etcdctl verion 출력됨
           * API version 출력됨

         * v2->v3 버전 변경

           `export ETCDTL_API=3 ./etcdctl version`

         * ETCDCTL v3

           `export ETCDTL_API=3 ./etcdctl version`

           set -> put로 바뀜



# ETCD in Kubernetes

1. Kubernetes에서 역할

   클러스터에 대한 정보 저장

   * Nodes
   * PODs
   * Configs
   * Secrets
   * Accounts
   * Roles
   * Bindings
   * Others

2. 배포 유형

   1. 수동 

      바이너리 직접 다운로드해 배포

      ```
      wget -q --https-only \"https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"
      ```

      

   2. kubeadm 이용

