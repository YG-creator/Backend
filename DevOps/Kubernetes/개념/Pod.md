# POD 

* 역할 

  POD : 앱의 단일 인스턴스

  container(앱 실행에 필요한것을 kubernetes 라는 물체로 캡슐 형태로 포장)로 구성

* 배치

  pod를 생성 후, container를 안에 배치함

  * 응용프로그램 규모를 키우기 위해서 기존 포드에 추가 컨테이너를 배치하지 않고 새로운 포드를 생성해서 그 안에 배치함

  * 멀티컨테이너 POD

    도우미 애플리케이션이 필요한 경우 한 Pod 안에 여러개 컨테이너 배치함 -> 저장소 공유, 시스템 공유함

  * 보통은 pod 하나에 컨테이너 하나임

* 배포 방법

  POD를 생성후 컨테이너 배포 

  1. POD 생성 후 Docker 이미지의 인스턴스 배포

     Docker Hub repository에서 이미지 다운로드해서 배포함

     `kubectl run nginx --image nginx`

  2. POD 확인하기

     `kubectl get pods`



# POD 생성하기

YAML 파일로 속성 설정 후 생성함

1. pod 설정 파일 생성

   `vim pod.yaml`

2. POD 속성 설정하기

   pod-definition.yml에서 설정

   * apiVersion : 생성 할 때 사용하는 쿠버네티스 API 버전

   * kind

     | kind       | apiVersion |
     | ---------- | ---------- |
     | Pod        | v1         |
     | Service    | v1         |
     | ReplicaSet | apps/v1    |
     | Deployment | apps/v1    |

   * metadata 

     * name : pod 이름
     * labels
       * app	: 앱이름
       * type or tier : 앱 타입(백엔드/프론트엔드)

   * spec : 추가 정보

     * containers
       * `- name` : 컨테이너 이름(ex : nginx-container)
       * image : 이미지 이름(ex : nginx)

3. pod 파일 저장하기

   ESC : wq

4. POD 생성하기

   `kubectl create -f 파일이름.yml`

   create = apply

5. 확인하기

   * POD 확인하기

     `kuectl get pods`

   * 세부사항 보기

     `kubectl describe pod pod이름`



# POD 실습

1. image로 pod 생성하기

`kubectl run pod이름 --image=image이름`

2. pod 갯수 세기

   `kubectl get pods`

3. 상세 확인

   * 무슨 이미지로 생성했는지 확인
   * 어떤 이미지로 생성했는지 확인
   * 컨테이너 상태 확인
   * 에러 이유

   `kubectl describe pod pod이름`

4. 어떤 노드에 배치됐는지 확인

   `kubectl get pods -o wide`

5. kubectl get pods에서 READY 의미

   실행중 pod/전체 pod

6. pod 삭제

   `kubectl delete pod pod이름`

7. YAML로 pod 생성하기

   1. 설정하기

      `kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml`

   2. pod 생성하기

      `kubectl create -f redis-definition.yaml`

   3. 확인하기

      `kubectl get pods`

8. 설정 수정하기

   1. 설정 파일 수정

      `vi 파일이름`

   2. 수정사항 적용하기

      `kubectl apply -f 파일이름.yaml `



# Replica Set

* 사용하는 이유

  * 고가용성 :  응용어플리케이션이 항상 실행되도록 하기 위해 포드를 복사함
  * 로드 밸런싱 : 서로 다른 노드의 여러 포드에 걸쳐 부하 분산

* Replication Controller와 차이점

  * selector

    복제품이 어느 포드를 모니터할지 알수 있음

* 생성법

  1. yaml파일 작성

     `vi 이름.yaml`

     * apiVersion : apps/v1

     * kind : ReplicaSet

     * metadata 

     * spec

       * template

         apiVersion,kind를 제외한 pod yaml파일 내용

       * replicas

         복제 갯수

       * selector

         모니터링 할 포드

  2. 생성

     `kubectl create -f rs이름.yaml`

  3. 확인

     `kubectl get rs`

* 삭제

  `kubectl delete rs rs이름`

* 수정

  * Yaml 파일 수정

    `vi 파일이름.yaml`  > `kubectl replace -f 파일이름.yaml`

  * `kubectl edit rs rs이름`

* scale 변경하기

  * scale 사용하기

    `kubectl scale rs rs이름 --replicas=크기`

  * 파일 수정하기

    `vi 이름.yaml` > replicas 값 수정 > `kubectl replace -f 이름.yaml`



* 주의점

  matchlLabels - tier = template - metadata - tier 

* rs = replicasets

* image 잘못 설정해서 replicaset 생성안될때

  kubectl edit rs

  Image 수정

  기존 포드 삭제 해줘야 됨



# Deployment

1. 생성 방법

   1. 배포 정의 파일 작성

      replicaSet 과 유사, kind만 Deployment 로 다름

   2. 생성 > 자동으로 replicaset, pods 생성 됨

      `kubectl create -f 이름.yaml`

   3. 확인

      `kubectl get all`

2. Tip

   **Create an NGINX Pod**

   ```
   kubectl run nginx --image=nginx
   ```

   **Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)**

   ```
   kubectl run nginx --image=nginx --dry-run=client -o yaml
   ```

   **Create a deployment**

   ```
   kubectl create deployment --image=nginx nginx
   ```

   **Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)**

   ```
   kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
   ```

   **Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.**

   ```
   kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
   ```

   **Make necessary changes to the file (for example, adding more replicas) and then create the deployment.**

   ```
   kubectl create -f nginx-deployment.yaml
   ```

   

   **OR**

   **In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.**

   ```
   kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
   ```

   

# Service

* 역할

  애플리케이션 안팎의 다양한 구성 요소간의 통신을 가능하게 함

  

* 종류

  * NodePort

    * 역할

      노드의 포트와 내부 포트 매핑 + 내부 포트와 Pod IP 매핑

      ssh로 Node 접속한 뒤 내부 Pod로 접속할 필요 없이 바로 Node에 접속하면 애플리케이션 실행 됨

    * 생성 방법

      1. yaml파일 작성

         ```bash
         apiVersion: v1
         kind: Service
         metadata:
         	name: myapp-service
         
         spec:
         	type: NodePort
         	ports:	// nodePort와 targetPort 매핑
         	 - targetPort:80    	
         	   port: 80					
         	   nodePort: 30008
           selector:	// targetPort 와 Pod IP 매핑
           	pod설정 파일에서 metadata의 labels 하위 값들을 잘라내서 붙여넣기
         ```

      2. Service 생성

         `kubectl create -f 이름.yaml`

      3. 확인

         `kubectl get services`

      4. 접속 해보기

         `curl http://{node의 IP}:{nodePort}`

    * 멀티 Pod인 경우

      자동으로 매핑함 by selector

      * pod설정 파일에서 labels 하위key-value을 잘라내서 selector에 붙여놓으면 됨
      * 부하를 분산함

    * 장점

      Pod 업데이트 시 자동으로 서비스도 업데이트 됨

  * ClusterIP

    * 역할

      * 가상 IP를 만들어서 서비스 간의 통신을 가능하게 함

      * 중간다리 역할을 해서 Pod를 변경해도 서비스 간 통신에 영향을 안 미치게 됨

        Ex) frontend - backend - DB 간 통신

    * 생성 방법

      1. yaml파일 작성

         ```bash
         apiVersion: v1
         kind: Service
         metadata:
         	name: back-end
         
         spec:
         	type: ClusterIP
         	ports:	// 
         	 - targetPort:80    	
         	   port: 80					
           selector:	// targetPort 와 Pod IP 매핑
           	pod설정 파일에서 metadata의 labels 하위 값들을 잘라내서 붙여넣기
         ```

      2. Service 생성

         `kubectl create -f 이름.yaml`

      3. 확인

         `kubectl get services`

  * LoadBalancer

    * 역할

      사용자에게 단일 IP를 제공 + 알아서 부하 분산

    * 주의점

      다 되는 건 아님(AWS, GCP, Azure 은 됨)

    * 생성 방법

      NodePort와 유사함

      1. yaml파일 작성

         ```bash
         apiVersion: v1
         kind: Service
         metadata:
         	name: myapp-service
         
         spec:
         	type: LoadBalancer
         	ports:	// nodePort와 targetPort 매핑
         	 - targetPort:80    	
         	   port: 80					
         	   nodePort: 30008
           selector:	// targetPort 와 Pod IP 매핑
           	pod설정 파일에서 metadata의 labels 하위 값들을 잘라내서 붙여넣기
         ```

      2. Service 생성

         `kubectl create -f 이름.yaml`

      3. 확인

         `kubectl get services`

      4. 접속 해보기

         `curl http://{node의 IP}:{nodePort}`



## 실습

Type 확인

serivce 갯수 확인

`kubectl get services`



targetport 확인

label 갯수 확인

endpoint 확인

`kubectl describe service`



Deployments 갯수 확인

`kubectl get deployments`



imgage 확인

`kubectl describe deployments`



서비스 생성

1. Yaml 수정
2. 생성



# Namespace

* 역할

  오브젝트를 논리적으로 구분하여 그룹핑 함

* 옵션

  * 옵션 넣기

    * CRUD 시 `--namespace={이름}`
    * Yaml 파일 metadata > namespace 에 값 넣으면 옵션 추가 안해도 됨

  * namespace 옮기기

    `kubectl config set-context $(kubectl config current-context) --namespace={namespace이름}`

  

* 생성법

  * Yaml 파일로 생성

    1. yaml 파일 작성

       ```
       apiVersion: v1
       kind: Namespace
       metadata: 
       	name: dev
       ```

    2. namespace 생성

       `kubectl create -f {이름}.yaml`

  * 명령어

    `kubectl create namespace {이름}`

    

* Resource Quota

  * 역할

    리소스 할당량 제한 역할

  * 생성 방법

    * Yaml 파일로 작성

      ```
      apiVersion: v1
      kind: ResourceQuota
      metadata:	// 이름, namespace 설정
      	name: compute-quota
      	namespace: dev	
      	
      spec:	// 리소스 제한
      	hard:
      		pods: "10"
      		requests.cpu: "4"
      		requests.memory: 5Gi
      		limits.cpu: "10"
      		limits.memory: 10Gi
      ```

      

## 실습

namespace 갯수 확인



조회 option



모든 Namespace 마다 조회 옵션



다른 namespace 방문

--이름.{namespace이름}.svc.



-n

ns

-A



현재 namespace service에 접속하기

{DNS}:{port} 로 접속 가능



다른 namespace service에 접속하기

{DNS}.{namespace이름}.svc.{domain} 로 접속 가능

`Db-service.dev.svc.cluster.local`



# Imperative vs Delcarative

* Imperative

  단계별로 어떻게 할지 지시

  create objects

  update objects

* Declarative

  최종 목적지만 선언

  파일 수정하면 알아서 반영함

  `kubectl apply -f 이름.yaml`

  

1. 문서이력 적기

2. 동일 체크명 체크키 다른 경우, 합치기
3. 빨간색 어케함?
4. 항목화
5. 항목별 가나다 순
