3_Scheduling

# Scheduling

### Manual Schedule
- 스케줄러는 생성된 Pod를 사용가능한 Worker Node에 할당	
    - 스케줄러가 제대로 동작하지 않는다면 Node가 할당되지 않음
- yaml 파일에서는 NodeName 값을 설정하지 않으면 Binding 객체를 만들어 할당한다	
    - 만약 NodeName을 지정하면 해당 노드에 할당됨 `spec > nodeName`
    - Binding 객체  {바인딩 yaml 파일 붙이기}

### Label & Selector
- Label	
    - 항목에 대한 속성 (key:value)	
    - 유형별로 개체를 그룹화할 수 있음 (app, function 등)	
    - `metadata > labels`에 추가
- Selector	
    - Label을 통해서 원하는 리소스를 선택		
    - `kubectl get pods --selector app=App1,env=dev`	
    - Replicaset의 경우 `matchLabels`과 `labels` 값을 일치시켜 Reaplicaset과 Pod를 연결
        ```yaml
        apiVersion: apps/v1
        kind: ReplicaSet
        metadata:
            name: webapp-1
            labels:
                app: App1
                function: Front-end
        spec:
            replicas: 3
            selector:
                matchLabels:
                    app: App1
            template:
                metadata:
                    labels:
                        app: App1
                        function: Front-end
                spec:
                    containers:
                        - name: simple-webapp
                          image: simple-webapp
        ```
- annotation
    - 기타 세부 사항을 기록 (이름, 버전, 빌드 정보 등)

## Node & Pod
Node에 Pod가 어떻게 스케줄링되는지 알아보자

### Taints & Tolerations
- Node에 Pod를 배치하는 전략
    - 어떤 Pod가 어떤 Node에 배치되는지, 제한 되는지에 관함
- `Taint`
    - Node에 설정함으로 원치않는 Pod가 할당되는 것을 방지
    - `kubectl taint nodes {node-name} {key}={value}:{taint-effect}`   
        `kubectl taint nodes node1 app=blue:NoSchedule`
        - NoSchedule: key-value에 맞지 않는 Pod를 스케줄하지 않음
        - preferNoSchedule: 스케줄을 하지 않는 것이 선호되지만, 보장하지 않음
        - NoExecute: 새 포드가 노드에 생성되지 않음, 만약 기존에 있다면 제거됨
    - Master Node의 경우, Pod를 예약하지 않도록 Taints가 설정되어 있음
- `Toleration`
    - Pod에 설정함으로써 Taint를 용인할 수 있는 Toleration 설정
    - Toleration이 설정되었다고 해당 Taint가 있는 노드에 반드시 할당되는 것은 아님
    ```yaml
    spec:
        containers:
        - name: nginx-container
          image: nginx
        tolerations:
        - key: "app"
          operator: "Equal"
          value: "blue"
          effect: "NoSchedule"
    ```

### Node Selector
- 특정 Pod가 특정 Node에서만 실행될 수 있도록 쉽게 설정
    - 예) 리소스를 많이 점유하는 Pod를 리소스가 많은 Node에서 실행하도록 한다
- 설정
    - node에 label 설정 `kubectl label nodes node-1 size=Lareg`
    ```yaml
    spec:
        nodeSelector:
            size: Large
    ```

### Node Affinity
- Pod가 특정 노드에서 호스팅되도록 설정
    - Node Selector에 비해 고급 기능 지원
    - Node Selector에 비해 복잡
    ```yaml
    spec: 
        affinity:
            nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchExpressions:
                        # Size 값이 Large 또는 Medium인 Node에 할당
                        - key: size
                          operator: In
                          values:
                          - Large
                          - Medium
    ```
- Operator: key값과 value값에 대한 관계 정의
    ```yaml
    # size 값이 Small이 아님
    - key: size
      operator: NotIn
      value:
        - Small

    # size 값이 존재한다
    - key: size
      operator: Exists
    ```
- Node Affinity Types: Pod의 타입 수명 주기 단계를 설정
    - DuringScheduling: Pod가 존재하지 않는 상태
    - DuringExecution: Pod가 생성된 상태
    - Available
        - `requiredDuringSchedulingIgnoredDuringExecution`
            - 원하는 노드가 없다면 스케줄링이 되지 않음
            - 실행 중 노드가 변경되어 원하는 노드가 아니더라도 무시함
        - `preferredDuringSchedulingIgnoredDuringExecution`
            - 원하는 노드가 없어도 스케줄링이 진행됨 
            - 실행 중 노드가 변경되어 원하는 노드가 아니더라도 무시함
    - Planned
        - `requiredDuringSchedulingRequiredDuringExecution`
            - 원하는 노드가 없다면 스케줄링이 되지 않음
            - 실행 중 노드가 변경되면 Pod를 제거

#### Taints & Tolerations // Node Affinity
1. Taints & Tolerations만 설정?
    - 아무것도 설정되지 않은 Pod가 노드에 할당되지 못 함
    - Toleration가 설정된 Pod가 아무것도 설정되지 않은 노드에 할당될 수 있음
1. Node Affinity만 설정?
    - 설정된 노드와 Pod가 잘 매핑됨
    - 아무것도 설정되지 않은 Pod가 해당 노드에 할당될 수 있음
1. 모두 설정!
    - 원하는 노드에 원하는 Pod가 할당될 수 있는 최적의 방법

### 리소스 제한
- 노드가 CPU, MEM, DISK 등을 관리한다 (requests, limits)
    - 기본적으로 0.5 CPU / 256Mi MEM
    - CPU 초과 > CPU가 초과하지 않도록 조절
    - MEM 초과 > Pod 종료
- 만약 제한된 리소스에 초과되는 Pod는 노드에 할당되지 못 함
    - 6CPU 노드에서 5CPU가 할당된 상태에서 2CPU가 요구되는 Pod가 할당되려고 할 때
    - 할당할 노드가 없다면 스케줄링 예약됨 (Pending)
- 1 CPU, 1Gi MEM
    ```yaml
    spec:
        containers:
        - name: simple-webapp-color
          image: simple-webapp-color
          resources:
            requests:
                memory: "1Gi"
                cpu: 1
            limits:
                memory: "2Gi"
                cpu: 2
    ```
- `LimitRange`: 만약 생성되는 Pod의 기본값을 변경
    ```yaml
    apiVersion: v1
    kind: LimitRange
    metadata:
        name: mem-limit-range
    spec:
        limits:
        - default:
            memory: 512Mi
        defaultRequest:
            memory: 256Mi
        type: Container
    ```

### Daemon Sets
- Pod의 여러 인스턴스를 배포하는 데 도움
    - 클러스터의 각 노드에서 하나의 Pod만 복사됨
    - 노드가 생성된다면 복제본이 자동 추가됨
    - Scheduler와 독립적
- Monitoring Agent, Logs Viewer, Networking 등에 이용
- yaml이 ReplicaSets와 유사
    ```yaml
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
        name: fluentd-elasticsearch
    namespace: kube-system
    labels:
        k8s-app: fluentd-logging
    spec:
        selector:
            matchLabels:
            name: fluentd-elasticsearch
        template:
            metadata:
            labels:
                name: fluentd-elasticsearch
            spec:
                containers:
                - name: fluentd-elasticsearch
                  image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
    ```

### Static Pods
- kube API 서버 등의 K8S의 요소의 개입없이 만들어지는 Pod
- 모든 워커 노드는 `kubelet` `Docker`을 가짐
    - kubelet은 kube API 서버없이 `Pod`를 생성할 수 있음
    - 워커 노드의 Directory에 yaml 파일 생성 > kubelet이 주기적으로 확인 후 Pod 생성
        - yaml 파일을 수정하면 Pod를 다시 생성
        - yaml 파일을 삭제하면 Pod를 제거
    - `docker ps`를 통해서 확인 가능
    - Kube API를 통해서 편집 불가능 (읽기 전용)
    - Scheduler에 독립적
- 경로 설정
    - `kubelet.service`에서 설정 가능
        1. `--pod-manifest-path=/etc/Kubernetes/manifests`
        1. `--config=kubeconfig.yaml` > `staticPodPath: /etc/Kubernetes/manifests`
- 예시
    - 마스터 노드에서 필요한 Pod들을 미리 설정한다 (독립적임으로 충돌이 없음)