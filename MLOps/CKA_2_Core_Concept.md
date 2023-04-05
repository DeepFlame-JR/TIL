CKA_2_Core_Concept

# 1. Introduce

### CKA (Certified Kubernetes Administrator)
- 쿠버네티스 관리자 인증 시험
    - 실습 위주로 되어있음
    - 기술의 작동 방식을 알아야함
    - HA Deolyment, Logging/Monitoring, Maintenance 등을 다룸
- 참고 사이트
    - Certified Kubernetes Administrator: https://www.cncf.io/certification/cka/
    - Exam Curriculum (Topics): https://github.com/cncf/curriculum
    - Candidate Handbook: https://www.cncf.io/certification/candidate-handbook
    - Exam Tips: http://training.linuxfoundation.org/go//Important-Tips-CKA-CKAD


# 2. Core Concepts

## Cluster Architecture
- Master Node: Kubernetes 관리를 담당
    - ETCD Cluster
        - 클러스터에 대한 정보를 저장 (어떤 컨테이너가 어떤 노드에 있는지 등)
        - 어떤 컨테이너가 어떤 선박에 있는지 등의 정보를 저장
        - Key-Value DB 
    - kube-scheduler    
        - 노드에서 애플리케이션과 컨테이너를 예약
        - 워커 노드의 컨디션, 로드할 수 있는 컨테이너 유형 등을 확인
    - Controll-Manager
        - 새로운 노드에 적재 및 컨테이너에 장애가 발생하지는 않는지 모니터링
    - kube-api 서버
        - 클러스터 내 모든 작업을 조정 (노드 제어, 복제, 컨트롤 등)
    - kubelet
        - kube-api 서버와 통신
        - kube-api 서버의 지시를 듣고, 노드에서 컨테이너를 생성하거나 제거
        - 노드, 컨테이너 상태 모니터링 
    - kube-proxy
        - 워커 노드 간에 통신 활성화
- Worker Node: 컨테이너를 로드, Docker 등의 런타임을 통해서 

### ETCD
- key-value 형태의 핵심 저장소
    - 빠른 읽기와 쓰기 제공
- 클러스터의 Nodes, PODs, Configs, Secrets, Accounts, Roles 등을 가짐
    - 클러스터 내 모든 작업을 업데이트
    - 처음부터 설정하기 vs kubeadm 도구 활용 가능
    - `etcd.service` 파일에서 많은 옵션을 설정 가능
- 여러 클러스터와 마스터가 있는 고가용성 환경
    - 여러 ETCD 인스턴스가 마스터 노드에 분산됨
    - `etcd.service` 파일에서 구성
- 명령어
```powershell
--cacert /etc/kubernetes/pki/etcd/ca.crt     
--cert /etc/kubernetes/pki/etcd/server.crt     
--key /etc/kubernetes/pki/etcd/server.key

kubectl exec etcd-master -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt  --key /etc/kubernetes/pki/etcd/server.key" 
```

### kube-api 서버
- 클러스터 내 모든 작업의 중심
- 과정 (Pod create)
    1. 유저 인증
    1. 요청 유효성 검사
    1. 정보 조회
    1. ETCD 업데이트
    1. 스케줄러에 등록 
    1. 적절한 워크 노드의 kubelet에 할당
- `kube-apiserver.service`를 통해서 구성 요소를 설정
    - 인증을 위한 SSL/TLS 인증서 설정
    - etcd 서버 정보 설정

#### Kube Controller Manager
- 시스템 내의 다양한 구성 요소 상태를 지속적으로 모니터링
    - 전체 시스템을 원하는 상태로 만들기 위해서 노력
    - 노드의 상태를 지속적으로 확인하고, 이에 대한 조치를 kube-api 서버를 통해 수행
    - 워커 노드에 장애가 발생하면 노드를 하난 프로비저닝하고, 컨테이너를 이동
- 수많은 Controller를 포함
    - Replication, Node, Job, Deployment, Namespace 등 
- `kube-controller-manager.service`를 통해서 구성 요소 설정

#### Kube Scheduler
- Pod가 배치되는 노드를 결정
    - Pod가 특정 리소스를 요구하는지 확인 → 가장 적합한 노드를 선별
        1. Filter Nodes: 특정 리소스에 해당하지 않는 노드를 필터
        1. Rank Nodes: 우선 순위 기능을 사용하여 순위를 매김
    - 기준은 사용자에 따라 변경할 수 있음
    - 직접 노드에 Pod를 할당하지는 않음 (kubelet의 역할)
- `kube-scheduler.service`를 통해서 구성 요소 결정

#### Kubelet
- 워커 노드에 위치하여, 스케줄러가 지시한 대로 컨테이너 관리
- 모니터링 및 kube-api에 보고
- 워커 노드에 수동으로 설치해야함

#### kube-proxy
- 모든 Pod 모든 Pod에 연결될 수 있도록 한 네트워크
    - IP는 바뀔 수 있음 → 그럼 서비스로 접근해야하는데, 서비스는 실체가 아님 → Kube-proxy
    - 각 노드에서 생성되는 프로세스
    - 지속적으로 새로운 서비스를 찾음 (IPTABLES 방식에 의하면 서비스와 IP를 연결)

## 실습

#### 명령어
`kubectl [COMMAND] [TYPE] [NAME] [FLAGS] `
1. COMMAND: create, get, describe 와 같은 operation 종류
    - get: 리소스 리스트를 출력할 때 사용
    - describe: 리소스와 name에 해당하는 자세한 정보 출력(IP, 작업내용 등)
    - edit : Pod, Service, PVC 등 resource에 설정을 변경할 때 사용
    - create: resource를 생성할 때 사용
    - apply: resource를 적용할 때 사용
    - delete: resource를 제거할 때 사용
    - top: node의 cpu, memory 사용량 확인
1. TYPE : pods, nodes 등의 타입
    - pods, nodes, services(svc), storageclass(sc), pvc(영구볼륨클레임), pv(영구볼륨)
    - https://kubernetes.io/docs/reference/kubectl/overview/#resource-types
- NAME : 특정 리소스의 이름 선언
- FLAGS : 추가적인 옵션 선언

``` powershell
kubectl get pods
kubectl get pods wc -l  # 행의 개수
kubectl get pods -o wide  # 더 많은 열 표시
kubectl get pods -A  # 모든 NameSpace에 있는 pods 표시

kubectl run nginx --image nginx
kubectl describe pod newpods

# 해당 명령어를 실행하는 yaml 샘플 파일을 만든다
kubectl run redis --image=redis --dry-run=client -o yaml > redis - production.yaml

kubectl create -f redis-production.yaml # -f (file)
kubectl apply -f redis-production.yaml

# 강제로 해당 yaml에 있는 내용을 반영한다
kubectl replace --force -f kubectl-edit-1764306979.yaml
```

### POD
- 컨테이너를 하나 이상 모아놓은 것. 쿠버네티스 애플리케이션의 최소 단위
    - 쿠버네티스는 워커노드에 컨테이너를 직접 배포하지 않음 → Pod로 캡슐화하여 배포
- 오토 스케일링 시, Pod를 증가시킴으로써 인스턴스를 증가시킴 (포드에 인스턴스를 추가하지 않음)
- 만약 인스턴스끼리 도움이 필요한 경우에는 동일한 포드로 묶일 수 있음
    - 로컬로 네트워크와 볼륨을 공유할 수 있음
    - 함께 생성되거나, 제거됨
- Pod 생성
    ```powershell
    kubectl run nginx --image nginx  # Pod 생성
    kubectl get pods  # Pod 확인
    ```  
- yaml파일로 구성하기
    ```yaml
    # pod-definition.yml
    apiVersion: v1
    kind: Pod   # 만드려는 객체의 유형 (Replicaset, Deplyment 등이 올 수 있음)
    metadata:   # 객체에 대한 데이터
        name: myapp-pod
        labels: # 검색을 위해 중요함 (키 값에 제약이 없이 입력할 수 있음)
            app: myapp
            type: front-end
    spec:  # 사양 섹션 (Kubernetes의 추가 정보를 제공)
        containers: # List
        - name: nginx-container
          image: nginx
        - name: busybox
          image: busybox
    ```

    ```powershell
    kubectl create -f pod-definition.yml
    kubectl get pods
    kubectl describe pod myapp-pod
    ```

### Replication Controller
- 클러스터의 여러 노드에 걸쳐있음
    - 여러 Pod 간에 균형을 맞추는 것에 도움
- 이유
    - 기존 Pod가 실패했을 때, 새 Pod를 자동으로 불러옴
    - 지정된 수의 Pod가 실행 중인지 보장
    - 여러 Pod를 만들어 로드를 공유
    - 이용자가 늘어난다면 부하의 균형을 맞추기 위해 추가 Pod를 배포

```yaml
# rc-definition.yaml
apiVersion: v1
kind: ReplicationController
metadata:
    name: myapp-rc
    labels:
        app: myapp
        type: front-end
spec:
    template:
        # pod-definition.yaml 일부 가져옴
        metadata:
            name: myapp-pod
            labels:
                app: myapp
                type: front-end
        spec:
            containers: # List
            - name: nginx-container
            image: nginx
        replicas: 3
``` 

```powershell
kubectl create - f rc-definition.yaml
kubectl get replicationcontroller
kubectl get pods
```

### ReplicaSet
- 새로운 복제 권장 방식
    - Replication Controller와 유사하지만, 다름
- 아래에 있는 Pod 정의 및 컨트롤 가능
    - 기존 Pod를 모니터링하는 데에 활용 가능
    - Pod 정의했을 때, meta data를 활용해서 탐색한 것 처럼 selector로 탐색 가능
    - matchLabels의 경우, template 내 labels 값 중 하나의 값을 가져야함

```yaml
# replicaset-definition.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: myapp-rs
    labels:
        app: myapp
        type: front-end
spec:
    template:
        # pod-definition.yaml 일부 가져옴
        metadata:
            name: myapp-pod
            labels:
                app: myapp
                type: front-end
        spec:
            containers: # List
            - name: nginx-container
            image: nginx
        replicas: 3
selector:
    matchLabels:
        type: front-end
``` 

```powershell
kubectl create - f replicaset - definition.yaml
kubectl get replicaset
kubectl get rs
kubectl get pods

# ReplicaSet 내 Pod 정보
kubectl describe pod pod-name
# ReplicaSet 의 정보 변경
kubectl edit replicaset rs-name
```

#### Scale
- 복제본 수 업데이트 
    1. yaml 파일에서 replicas를 6으로 변경 > `kubectl replace - f replicaset - definition.yaml`
    1. `kubectl scale--replicas = 6 - f replicaset - definition.yaml`
    1. `kubectl scale--replicas = 6 replicaset myapp - replicaset`

### Deployment
- 새롭게 개발된 버전을 배포하는 방법이 필요
    - 변경사항을 반영 후, 한꺼번에 POD를 껐다가 켤 수도 있음
- 롤링 업데이트
    - 인스턴스 하나하나씩 업데이트 진행한다
    - 한꺼번에 업데이트했을 때 나타날 에러 방지
- **Deployment > Replica Set > POD(캡슐화된 인스턴스)**

```yaml
# deplyment-definition.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: myapp-deployment
    labels:
        app: myapp
        type: front-end
spec:
    template:
        # pod-definition.yaml 일부 가져옴
        metadata:
            name: myapp-pod
            labels:
                app: myapp
                type: front-end
        spec:
            containers: # List
            - name: nginx-container
            image: nginx
        replicas: 3
selector:
    matchLabels:
        type: front-end
``` 

```powershell
kubectl create - f deployment-definition.yaml
kubectl get deployments
kubectl get rs
kubectl get pods

kubectl get all
```

### Services
- 애플리케이션 내부와 외부의 다양한 구성 요소 간에 통신 가능
- 프론트엔드, 백엔드, DB Pod의 연결 도움
- 종류
    1. NodePort: 내부 포트를 만들어 노드에 액세스 할 수 있도록 함
        - NodePort > Port > TargetPort (노드 포트를 Pod의 포트로 매핑)
        - 노드에 다수의 Pod가 존재해도 알아서 부하를 분산함 (label은 같음)
        - 다수 노드에 분포하더라도 알아서 부하를 분산함
        ```yaml
        spec:
            type: NodePort
        ports:
            - targetProt: 80
            port: 80  # 필수
            nodePort: 30008  # 지정하지 않을 시에 가능한 포트 중 랜덤으로 할당됨
        selector:  # Pod 식별
            app: myapp
            type: front-end
        ```

    1. ClusterIP: 서비스가 가상 IP를 생성하여 서로 다른 서비스의 통신을 가능하게 함
        - 프론트엔드 Pods > (Cluster IP) > 백엔드 Pods > (Cluster IP) > DB Pods
        ```yaml
        spec:
            type: ClusterIP  # Serivce의 기본 유형
        ports:
            - targetProt: 80
            port: 80  # service export port
        selector:  # Pod 식별
            app: myapp
            type: back-end
        ```
    
    1. LoadBalancer: 프로비저닝을 통해 부하를 분산
        - AWS, GCP에서 지원함
        ```yaml
        spec:
            type: LoadBalancer
        ports:
            - targetProt: 80
            port: 80  # 필수
            nodePort: 30008
        ```

### Namespace
- Cluster, Deployments, Pods 등 모든 요소들을 포함하는 독립적인 공간
    - 처음 생성될 때 Pod, Service를 생성
    - 각 Namespace에 고유한 정책 집합이 있을 수 있음
    - 기업 또는 운영 목적일 때, 고려할 수 있음
- 다른 Namespace를 참조하기 위해서는 `servicename.namespace.svc.cluster.local` 형식을 따름
    - 예 `mysql.connect("db-service.dev.svc.cluster.local")`
- 명령어 뒤에 `--namespace={namespaceName}` `-n={namespaceName}` 을 붙여 다른 namespace를 컨트롤할 수 있음
    - `kubectl get pods --namespace=dev`
    - `kubectl create -f pod-definition.yml --namespace=dev`
    - 또는 yml 파일의 metadata에 포함
        ```yaml
        metadata:
            name: myapp-pod
            namespace: dev
        ```
    - 전체보기 `kubectl get pods --all-namespaces`
- 기본적으로 default로 설정됨
    - 전환 `kubectl config set-context $(kubectl config current-context) -n=dev`
- ResourceQuota: 다수 노드에서 namespace가 리소스를 일정한 할당량만 사용하도록 설정
    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
        name: compute-quota
        namespace: dev
    spec:
        hard:
            pods: "10"
            requests.cpu: "4"
            requests.memory: 5Gi
            limits.cpu: "10"
            limits.memory: 10Gi
    ```

### Imperative, Declarative
1. Imperative (명령형 방식)
    - `어떻게 할것인가`가 우선
        - 단계별 지침을 받고 따름
    - 단계 실행 중에 발생하는 예외상황에 취약
    - 명령어를 유지하기 어렵기 때문에 복잡한 환경에 취약
    - 예시
        ```
        1. `web-server` VM을 프로비저닝해라
        2. NGINX를 설치해라
        3. 포트를 8080으로 설정해라 
        ...
        ```
    - 명령어

1. Declarative (선언형 방식)
    - `무엇을 할 것인가`가 우선
        - 최종 결과만 선언하고, 단계별 지침을 제공하지 않음
    - 필요한 변경사항만 변경하고, 나머지는 시스템에 맡김
    - 예시
        ```
        VM Name: web-server
        package: nginx
        Port: 8080
        ... 
        ```
    
#### KubeFlow에서의 Imperative, Declarative

```powershell
# Imperative

# Create Objects
kubectl create -f nginx.yaml

# Edit
kubectl edit deplyment nginx
kubectl replace -f nginx.yaml
kubectl replace --force -f nginx:yaml

# Error
# 같은 내용이 반복되면 객체가 이미 존재하기 때문에 에러가 나타난다
kubectl create -f nginx.yaml

# Declarative
# Create Objects
# 객체가 존재하지 않을 때 생성
kubectl apply -f nginx.yaml

# Edit
# yaml의 업데이트 내용을 적용함
kubectl apply -f nginx.yaml
```

## Tip for Command
1. POD
    ```powershell
    kubectl run nginx --image=nginx
    kubectl run nginx --image=nginx --dry-run=client -o yaml > pod-definition.yaml
    ```
1. Deployment
    ```powershell
    kubectl create deployment nginx --image=nginx
    kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
    kubectl create deployment nginx --image=nginx --replicas=4

    # replica 4
    kubectl scale deployment nginx --replicas=4
    ```
1. Service
    ```powershell
    # type 미지정 시, Cluster IP
    kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
    kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml
    ```

#### 약어
`kubectl api-resources`로 확인 가능

NAME|SHORTNAMES
--|--
namespaces|ns
nodes|no
pods|po
replicasets|rs
serviceaccounts|sa
services|svc
configmaps|cm
daemonsets|ds
deployments|deploy
horizontalpodautoscalers|hpa
ingresses|ing
persistentvolumeclaims|pvc
persistentvolumes|pv
