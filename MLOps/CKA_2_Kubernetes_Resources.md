CKA_2_Kubernetes_Resources

# 2. Kubernetes Resources

### kubectl 명령어
`kubectl [COMMAND] [TYPE] [NAME] [FLAGS] `
1. COMMAND: create, get, describe 와 같은 operation 종류
    - get: resource 리스트 출력
    - describe: resource와 name에 해당하는 자세한 정보 출력(IP, 작업내용 등)
    - edit : resource에 설정을 변경
    - create: resource를 생성
    - apply: resource를 적용
    - delete: resource를 제거
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

## Controller
- 클러스터의 상태를 지속적으로 모니터링, 원하는 상태를 유지하기 위해 여러 가지 컨트롤러를 kube-api 서버를 통해서 수행
- Pod의 생명주기를 관리하고, 복제, 롤아웃, 롤백, 스케일링 등의 작업을 수행하며 애클리케이션을 관리


### Pod
- 하나 이상의 컨테이너를 포함하는 단일 응용 프로그램 실행 환경
    - 쿠버네티스 애플리케이션의 최소 단위
    - 동일한 노드에서 실행되며 네트워크, 저장소 등의 리소스를 공유
- 오토 스케일링 시, Pod를 증가시킴으로써 인스턴스를 증가시킴 (포드에 인스턴스를 추가하지 않음)
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
    ```

    ```powershell
    kubectl create -f pod-definition.yml
    kubectl get pods
    kubectl describe pod myapp-pod
    ```

#### multi-container-pod
- 함께 필요한 서비스를 묶음
  - Micro Service 단위로 생각했을 때의 서비스 
  - 같은 네트워크, 볼륨 등을 공유함
- spec > containers 내에 많은 컨테이너를 선언함으로써 구현 가능
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: main-container
    image: nginx
    ports:
    - containerPort: 80
  - name: sidecar-container # side container가 main-container를 보조함
    image: busybox
    command: ["sleep", "3600"]
```

### Replication Controller
- 사용자가 지정한 복제 수에 따라 Pod의 복제본을 생성 및 관리 > `고가용성 보장`
    - 클러스터에는 여러 노드가 있음 > 한 노드에서 장애가 발생해도 다른 노드를 활용해서 애플리케이션이 실행됨을 보장할 수 있음
- 복제본의 상태를 지속적으로 모니터링하고, 실패한 복제본을 자동으로 감지하고 복구

```yaml
# rc-definition.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-replication-controller
spec:
  replicas: 3  # 복제본 개수
  selector:  # 복제할 pod를 선택 (명시하지 않으면 모든 pod가 복제됨)
    matchLabels:
        app: my-app
  template:  # 새로운 pod의 명세서
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
        - name: my-sidecar-container
          image: sidecar:latest
          ports:
            - containerPort: 8080
    # 복제되지 않으며, ReplicationController가 관리하는 집합과 독립적으로 동작
    - name: my-special-pod
      image: special-image:latest
      ports:
        - containerPort: 8080
``` 

```powershell
kubectl create - f rc-definition.yaml
kubectl get replicationcontroller
kubectl get pods
```

### ReplicaSet
- 새로운 복제 권장 방식 (Replication Controller와 유사)
- ReplicationController와 차이
    - 기존에는 `equality-based` 셀렉터였지만, 더 강력한 기능인 `set-based` 셀렉터 제공
    - 최근 Kubernetes 버전에서 사용가능함
    - 부분 복제 지원
        - ReplicaSet 생성 후에 생성된 Pod에서 `metadata.labels.exclude: "true"` 설정

```yaml
# replicaset-definition.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
        environment:
            matchExpressions:
            - {key: tier, operator: Exists}  # key에 tier가 존재하는지 확인
            - {key: app, operator: In, values: [my-app, web]}
            - {key: age, operator: NotIn, values: [18, 19]}
            - {key: version, operator: Regexp, values: [0-9]+.[0-9]+.[0-9]+}
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
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
- 애플리케이션 배포 및 업데이트 관리하는 Resource
    - RelicaSet 기반 (**Deployment > Replica Set > POD**)
    - 애플리케이션을 원하는 상태로 유지하면서 롤아웃 및 롤백과 같은 배포 전략 제공
        - 가용성, 사용자 영향 및 업데이트 속도 등을 고려하여 정의
    - 롤링 업데이트
        - 롤링 업데이트는 새로운 ReplicaSet을 생성하여 점진적으로 기존 ReplicaSet의 파드를 제거하고 새로운 ReplicaSet의 파드를 추가함으로써 이루어짐. 
        - 가용성을 유지
    - 롤백
        - Deployment는 이전 버전으로의 롤백을 지원
        - 업데이트 중 문제가 발생하거나 예상치 못한 동작이 발생한 경우, Deployment는 이전 버전으로 롤백하여 이전 상태로 복원
    - 히스토리 및 롤아웃 확인
        - Deployment는 이전 배포에 대한 히스토리 및 롤아웃 상태를 추적
        - 배포 이력을 확인하고 필요한 경우 특정 시점으로 롤백 가능
- ReplicaSet과 구조는 유사하지만, 다른 목적을 가짐
    - Deployment는 ReplicaSet을 생성하고 업데이트하는데 사용(롤링 업데이트, 롤백, 배포 전략 등과 같은 기능을 제공)되는 상위 수준의 개념
    - ReplicaSet은 파드 복제본을 관리하는 데 사용되는 하위 수준의 개념
    - 대부분의 경우 Deployment를 사용하여 애플리케이션을 배포하고 관리하는 것이 권장됨
```yaml
# deplyment-definition.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
``` 

```powershell
kubectl create - f deployment-definition.yaml
kubectl get deployments
kubectl get rs
kubectl get pods

kubectl get all
```

#### 롤링 업데이트
1. 새 ReplicaSet 생성: 이 ReplicaSet은 업데이트된 이미지 또는 설정을 사용
1. 파드 점진적 배포: 새로운 ReplicaSet이 생성된 후, Kubernetes는 점진적으로 새로운 파드를 생성하고 기존 ReplicaSet의 파드를 제거.이를 통해 애플리케이션의 가용성을 유지
1. 상태 확인: 파드의 준비 상태 및 정상 동작 여부를 확인하여 롤링 업데이트 중에 문제가 발생한 경우 자동으로 롤백
1. 롤백 및 완료: 만약 업데이트 중에 문제가 발생하거나 사용자가 업데이트를 롤백하고자 할 경우, Deployment는 이전 버전의 ReplicaSet으로 롤백하여 이전 상태로 복원있음. 업데이트가 정상적으로 완료되면 이전 버전의 ReplicaSet은 제거되고, 새로운 ReplicaSet이 애플리케이션의 현재 상태를 유지


## Service
- Pod의 네트워크 접근성을 제공하는 resource (안정성과 가용성을 향상)
  - 클러스터 외부로부터 요청을 받을 수 있도록 IP를 노출하는 역할
  - 기본적으로 클러스터 외부에서는 Pod에 접근할 수 없음
  - 동일한 레이블 셀렉터를 가진 Pod들의 그룹을 캡슐화하여 추상화 (단일 지점으로 많은 Pod를 동작시킬 수 있음)
- 주요 기능
    - 고정된 IP 주소
        - Service는 고유한 클러스터 내부 IP 주소를 가짐 (DNS 시스템 / etcd를 통해서 도메인 관리)
        - 파드가 생성, 삭제되어도 Service의 IP 주소는 유지됨
    - 로드 밸런싱
        - 여러 개의 파드가 있는 경우, Service는 파드들 사이에 요청을 분산하여 부하를 분담
        - 애플리케이션에 대한 트래픽을 균형있게 분배할 수 있습니다.
    - 서비스 디스커버리
        - 클라이언트 애플리케이션은 Service의 DNS 이름 또는 IP 주소를 사용하여 애플리케이션 내부의 파드에 접근
        - 파드의 IP 주소나 포트가 변경되더라도 클라이언트 애플리케이션은 변경 사항을 알 필요가 없습니다.
        - 디스커버리 메커니즘으로는 환경 변수, DNS 이름, 클러스터 내부 DNS 

<img src="https://user-images.githubusercontent.com/40620421/246141247-2705591f-5536-4b8d-a4e0-e8fd71c0b792.png" width="500">

### ClusterIP
- 가장 기본이 되는 Service 타입, 클러스터 내부 통신만 가능
- Service가 관리하는 Pod들에게 로드밸런싱

```yaml
# (외부) --10.0.171.239:80--> (서비스) --8080--> (Pod)

apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app.kubernetes.io/name: proxy
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
      - containerPort: 8080
        name: http-web-svc

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:  # label이 'app.kubernetes.io/name: proxy'를 가진 Pod를 대상으로함
    app.kubernetes.io/name: proxy
  ports:
  - name: name-of-service-port
    protocol: TCP
    # 기본적으로 그리고 편의상 `targetPort` 는 `port` 필드와 동일한 값으로 설정된다.
    port: 80  # 외부에서 Service에 접근할 때 사용되는 포트 번호
    targetPort: http-web-svc  # 선택한 Pod와 Service가 연결되는 포트 번호
```

### NodePort
- 클러스터 내부 및 외부 통신이 가능
- 노드 포트를 사용함 (30000-32767)
- type은 모두 중첩기능으로 설계되어, 각 단계에 더해지는 형태
    - 모든 노드에 동일한 포트를 열게됨 > Node마다 외부 트래픽을 받음 > ClusterIP로 모인 후 로드밸런싱

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - port: 80
      targetPort: 80
      # 선택적 필드 (명시하지 않은 경우 사용 가능한 포트를 자동으로 할당)
      # 기본적으로 그리고 편의상 쿠버네티스 컨트롤 플레인은 포트 범위에서 할당한다(기본값: 30000-32767)
      nodePort: 30007  # 만약 30007 포트가 사용 중이라면 실패
```

### LoadBalancer
- 기본적으로 외부에 존재. 외부 트래픽을 받는 역할
- 받는 트래픽을 각각의 Service로 전달 (L4 분배)
- LoadBalancer > NodePort > ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
  clusterIP: 10.0.171.239  # Cluster IP를 직접 선택
# 선택적 필드 (명시하지 않으면 자동으로 IP 지정)
# 외부에서 Service에 접근하기 위한 IP
status:
  loadBalancer:
    ingress:
    - ip: 192.0.2.127
```

### ExternalName
- 클러스터 내부에서 외부 도메인 이름을 사용하기 위해 사용
- 클러스터에서 `my-external-service`를 사용하여 외부 도메인에 접근할 수 있음
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  type: ExternalName
  externalName: example.com
```


### Namespace
- 클러스터 내 리소스를 그룹화하는 가상의 공간
  - 예를 들어, 개발, 스테이징, 프로덕션 환경을 위한 네임스페이스를 만듬
  - 각 리소스가 충돌하는 것을 방지
  - 액세스 제어 가능
  - 네트워크 분리
- ResourceQuota: 다수 노드에서 namespace가 리소스를 일정한 할당량만 사용하도록 설정
    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: example-quota
    spec:
      hard:
        pods: "5"                # 최대 허용할 Pod 개수
        requests.cpu: "1"        # CPU 요청량 제한
        requests.memory: 1Gi     # 메모리 요청량 제한
        limits.cpu: "2"          # CPU 제한
        limits.memory: 2Gi       # 메모리 제한
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
