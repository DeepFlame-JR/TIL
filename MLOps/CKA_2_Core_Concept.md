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
    - `kubectl get pod`와 같은 command도 모두 etcd에서 받는 내용
    - 만약 유실된다면 클러스터가 사용하는 모든 리소스가 미아가 됨
- 분산 저장소
    - 클러스터 형태로 여러 노드에 걸쳐 데이터를 저장 (고가용성 환경)
        - 여러 ETCD 인스턴스가 마스터 노드에 분산됨
    - 신뢰성과 일관성을 보장하기 위해서 `Raft 합의 알고리즘`사용
    - 컨센서스를 맞추는 것이 중요
        - Safety: 항상 올바른 결과를 리턴해야 합니다.
        - Available: 서버가 몇 대 다운되더라도 항상 응답해야 합니다.
        - Independent from timing: 네트워크 지연이 발생해도 로그의 일관성이 깨져서는 안됩니다.
        - Reactivity: 모든 서버에 복제되지 않았더라도 조건을 만족하면 빠르게 요청에 응답해야 합니다.

#### Raft 합의 알고리즘
- 구성 요소
    1. 리더(Leader): 클러스터의 상태를 관리하고 클라이언트의 요청을 처리하는 주 역할을 수행합니다.
    1. 팔로워(Follower): 리더의 상태를 따르며, 클라이언트 요청을 받기만 하고 처리하지는 않습니다.
    1. 후보자(Candidate): 새로운 리더를 선택하기 위해 선거에 참여합니다.
- 동작
    1. 주기적인 선거를 통해 리더를 선택 (election timeout이 끝난 노드가 후보자로 선정되어 선거를 요청)
    1. 리더는 클러스터의 합의를 이끌어내고, 변경 사항을 배포, 작업 스케줄링 (동기화를 통한 안정성과 일관성 유지)
    1. 리더가 동작 중인 동안에는 다른 노드가 팔로워로 동작
    1. 리더에 장애가 발생하면 선거가 다시 시작되어 새로운 리더를 선택
- 유의 사항
    1. 노드 개수를 홀수 개로 유지 > 리더 선출 과정이 안정적으로 동작
        - 후보자가 2이다? 투표 수가 홀수개가 됨

### kube-api 서버
- 클러스터 내의 모든 통신과 상호작용을 처리하는 api 서버
    - 사용자가 쿠버네티스와 상호작용할 수 있는 인터페이스를 제공
    - API 엔드포인트를 제공하며, 클러스터 관리, 리소스 생성, 애플리케이션 배포, 모니터링, 스케줄링 등 다양한 작업을 수행
    - 보안을 위해 인증(Authentication)과 권한 부여(Authorization)를 처리
- Pod create 시 진행 과정
    1. 유저 인증
    1. 요청 유효성 검사
    1. 정보 조회
    1. ETCD 업데이트
    1. 스케줄러에 등록 
    1. 적절한 워크 노드의 kubelet에 할당
- `kube-apiserver.service`를 통해서 구성 요소를 설정
    - 인증을 위한 SSL/TLS 인증서 설정
    - etcd 서버 정보 설정

#### API 서버
- 클라이언트 요청을 받아들이고, 해당 요청을 처리하여 클라이언트에게 응답을 반환
    - 보통 RESTful API를 구현하여 클라이언트와 상호작용. RESTful API는 일반적으로 HTTP 프로토콜을 사용하며, 요청과 응답은 JSON 또는 XML 형식으로 전송
- 주로 마이크로서비스 아키텍처 및 분산 시스템에서 사용
    - 마이크로 서비스 
        - 소프트웨어 어플리케이션을 나누는 작고 독립적인 단위
        - 각 서비스는 독립적으로 실행될 수 있음
        - 개별 서비스의 변경이 다른 서비스에 영향을 미치지 않고 배포 가능
        - API 서버를 통해 다른 마이크로서비스와 통신하여 상호작용


### Kube Controller Manager
- 다양한 컨트롤러들을 실행하고 관리하는 역할을 수행
- 클러스터의 상태를 지속적으로 모니터링, 원하는 상태를 유지하기 위해 여러 가지 컨트롤러를 kube-api 서버를 통해서 수행
- 종류 (Replication, Node, Job, Deployment, Namespace 등 )
    1. 레플리케이션 컨트롤러(Replication Controller)
        - 파드(Pod)의 복제본 수를 지속적으로 모니터링하고 관리
        - 지정된 복제본 수를 유지하기 위해 파드를 자동으로 생성, 수정 또는 삭제하여 애플리케이션의 가용성과 확장성을 관리
    1. 디플로이먼트 컨트롤러(Deployment Controller)
        - 디플로이먼트(Deployment) 리소스를 사용하여 애플리케이션의 배포와 롤링 업데이트를 관리
        - 새로운 버전의 애플리케이션을 점진적으로 배포하고 이전 버전의 애플리케이션을 천천히 제거하여 애플리케이션의 안정성을 유지
    1. 스테이트풀셋 컨트롤러(StatefulSet Controller)
        - 상태가 있는 애플리케이션(예: 데이터베이스)의 배포와 관리를 처리
        - 각 인스턴스에 고유한 식별자를 할당하고, 순차적인 배포 및 롤링 업데이트를 수행하여 데이터 일관성과 지속성을 유지
    1. 데몬셋 컨트롤러(DaemonSet Controller)
        - 각 워커 노드에 특정한 파드 인스턴스를 실행하는 데몬셋(DaemonSet)을 관리
        - 노드 수에 따라 파드 인스턴스가 자동으로 추가되거나 제거되므로, 클러스터의 모든 노드에 특정한 애플리케이션을 배치
- `kube-controller-manager.service`를 통해서 구성 요소 설정

#### StatefulSet
- 기존의 포드를 삭제하고 생성할 때 상태가 유지되지 않는 한계가 있음 (포드를 삭제하고 생성하면 완전히 새로운 가상환경이 시작됨)
- 필요에 따라 이러한 포드의 상태를 유지하고 싶을 수 있음 (응용프로그램의 로그나 기타 다른 정보들을 함께 저장하고자 하는 경우 단순히 PV를 하나 마운트해 이를 유지하기는 어려움)
- 스테이트풀셋으로 생성되는 포드는 영구 식별자를 가지고 상태를 유지할 수 있음
- 필요 케이스
    1. 안정적이고 고유한 네트워크 식별자가 필요한 경우
    1. 안정적이고 지속적인 스토리지를 사용해야 하는 경우
    1. 질서 정연한 포드의 배치와 확장을 원하는 경우
    1. 포드의 자동 롤링업데이트를 사용하기 원하는 경우

#### DemonSet
- 모든 노드 또는 특정한 노드에 특정한 파드(Pod)의 복사본이 실행
- 주로 로그 수집, 모니터링, 네트워킹과 같은 클러스터 전체적인 작업에 사용
- 노드가 클러스터에 추가되거나 제거될 때 자동으로 추가 또는 제거됨


### Kube Scheduler
- Pod의 요구 사항을 확인하고, 배치되는 노드를 결정
    - 최적의 노드에 할당하여 클러스터의 리소스 효율성을 향상, 노드 장애 시 자동으로 다른 노드로 옮기는 등의 고가용성 제공
    - 스케줄러는 파드의 예약 상태를 관리하고, 파드가 정상적으로 스케줄링되고 실행되도록 확인함. 
- Pod 요구 사항
    1. 리소스 요구 사항: 파드가 필요로 하는 CPU, 메모리 등의 리소스 양을 지정합니다.
    1. 볼륨 요구 사항: 파드가 사용하는 볼륨(예: 디스크, 네트워크 스토리지)의 종류, 크기, 마운트 포인트 등을 지정합니다. 
    1. Affinity/Anti-Affinity: 파드가 특정 노드와 함께 스케줄링되도록 하는 Affinity(친화성) 규칙이나 특정 노드와 함께 스케줄링되지 않도록 하는 Anti-Affinity(반친화성) 규칙을 정의할 수 있습니다. 이를 통해 파드 간의 관련성이나 격리성을 제어할 수 있습니다.
    1. Node Selector: 특정 노드에 레이블을 지정하고, 파드는 해당 노드에만 스케줄링될 수 있습니다.
    1. Node Affinity: 특정 노드와 관련된 규칙을 사용하여 파드를 할당할 수 있습니다. 
    1. 서비스 포트 포워딩: 파드에 외부로 노출되는 서비스 포트를 정의할 수 있습니다. 이를 통해 외부 클라이언트가 파드에 접근할 수 있게 됩니다.
- Node 선택
    - Filter Node: 적절한 노드 후보를 선별하는 과정
    - Rank Node: 후보 노드 중 최종적으로 Pod를 할당할 노드를 선택하는 과정 (후보 노드들을 점수화)

### Kubelet
- 클러스터의 각 노드에서 실행되는 에이전트 
- 역할
    1. Pod 실행: API 서버로부터 할당된 Pod목록을 받아와 노드에 실행
    1. Resource 관리: 노드의 리소스(CPU, 메모리, 디스크 등)를 모니터링하고 관리
    1. 상태 보고: 주기적으로 노드의 상태와 파드의 상태를 API 서버에 보고
    1. 라이프사이클 관리: 파드의 생성, 시작, 종료, 재시작 등의 이벤트를 감지하고 필요한 작업을 수행
    1. 볼륨 마운트: 파드에 정의된 볼륨(Volume)을 마운트하고, 컨테이너에 볼륨을 제공
    1. 보안 및 인증: API 서버와의 통신에 사용되는 인증 정보를 관리하고, 파드의 보안 정책을 적용

### kube-proxy
- 파드 간의 네트워크 연결 및 로드 밸런싱을 처리하여 클러스터 내의 서비스의 안정성과 가용성을 유지
    - 기본적으로 모든 노드에서 실행됨
    - 다른 노드 간의 Pod를 연결하는 것에 도움
- 주요 기능
    1. 서비스 디스커버리(Service Discovery)
        - 클러스터 내부에서 실행 중인 서비스의 IP 및 포트 정보를 파드에게 노출
        - 파드는 서비스를 사용할 수 있고, 서비스 간의 통신을 용이하게 함
    1. 로드 밸런싱(Load Balancing)
        - 클러스터 내부에서 동작하는 서비스에 대한 요청은 kube-proxy가 해당 서비스의 파드로 분산
        - 로드 밸런싱은 라운드 로빈 방식이나 IPVS 같은 로드 밸런서를 사용하여 구현
    1. 네트워크 프록시(Network Proxy)
        - 클러스터 내부의 파드는 내부 IP 주소를 사용하여 다른 파드와 통신

### POD가 실행되는 순서
1. 파드 정의 작성: 파드를 실행하기 위해 YAML 또는 JSON 형식으로 파드 정의 파일을 작성
1. `API 서버`와의 통신: 파드 정의 파일을 API 서버에 제출. API 서버는 파드를 클러스터의 상태 정보와 비교하여 유효성을 검사하고, 파드의 상태와 구성 정보를 저장.
1. 스케줄링: API 서버는 `스케줄러(Scheduler)`를 통해 파드를 할당할 노드를 선택
1. `Kubelet`에게 할당 요청: 선택된 노드의 Kubelet은 API 서버로부터 할당된 파드를 확인하고, 파드를 실행하기 위해 `컨테이너 런타임(Docker, containerd 등)`에게 실행 요청을 전달.
1. 컨테이너 실행: 컨테이너 런타임은 파드에 정의된 컨테이너 이미지를 가져와 컨테이너를 실행합니다. 이 때, Kubelet은 컨테이너의 라이프사이클 관리, 리소스 할당, 네트워크 설정 등을 담당.
1. 파드 상태 갱신: Kubelet은 파드의 상태를 주기적으로 감지하고, API 서버에 상태 업데이트를 보고합니다. 파드의 상태는 "Pending"에서 "Running" 또는 "Failed"로 변경될 수 있습니다.
1. 서비스 포트 포워딩: 만약 파드가 외부로 노출되어야 하는 서비스 포트를 정의했다면, Kube-proxy 컴포넌트는 해당 포트를 감시하고 요청을 파드로 포워딩합니다.


<img src="https://user-images.githubusercontent.com/40620421/244922843-9f6818fb-b324-4a47-b44a-d425ba7a9b49.png" width="600">


## 실습

### 명령어
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
