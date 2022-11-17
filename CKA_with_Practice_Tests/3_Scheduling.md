3_Scheduling

# Scheduling
- Kubernetes에 의해서 생성된 Pod가 어떤 방법으로 Node에 할당되는가
- 원하는 노드에 할당되도록 할 수 있는가?
- 기본 스케줄러 또는 확장 스케줄러를 활용할 수 있음

```powershell
k get nodes 
ssh [ip-address] >> 해당 노드로 이동할 수 있음
```

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
    - key: size
      operator: NotIn # size 값이 Small이 아님
      value:
        - Small
    
    - key: size
      operator: Exists # size 값이 존재한다
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
- kube API 서버 등의 K8S의 요소의 개입없이 노드에서 만들어지는 Pod
    - 이름에 마지막에는 노드 이름이 있음 (kube-apiserver-`controlplane`)
    - 특정 노드에서 만들어지기에 Scheduler에서 독립적
    - Kube API를 통해서 편집 불가능 (읽기 전용)
- kubelet은 kube API 서버없이 `Pod`를 생성할 수 있음
    - 모든 워커 노드는 `kubelet` `Docker`을 가짐
    - 워커 노드의 Directory에 yaml 파일 생성 > kubelet이 주기적으로 확인 후 Pod 생성
        - yaml 파일을 수정하면 Pod를 다시 생성
        - yaml 파일을 삭제하면 Pod를 제거
    - `docker ps`를 통해서 확인 가능
- 경로 설정
    - `kubelet.service`에서 설정 가능
        1. `--pod-manifest-path=/etc/Kubernetes/manifests`
        1. `--config=kubeconfig.yaml` > `staticPodPath: /etc/Kubernetes/manifests`
- 예시
    - 마스터 노드에서 필요한 Pod들을 미리 설정한다 (독립적임으로 충돌이 없음)


### Multiple Scheduler
- 특정 노드를 원하는 애플리케이션이 존재하는 경우
- 한 번에 여러 개의 스케줄러를 가질 수 있음
    - defualt-scheduler / my-scheduler / my-scheduler-2 등 ... 
- Scheduler가 Pod로 생성
- 방법  
    참고: https://kubernetes.io/docs/tasks/extend-kubernetes/configure-multiple-schedulers/
    1. `kube-scheduler.service` 파일을 다운로드   
        ```service
        ExecStart=/usr/local/bin/kube-scheduler \\
            --config=/etc/kubernetes/config/kube-scheduler-config.yaml
        ```
    1. Scheduler config 파일 구성 (Scheduler가 어떻게 동작할 것인지 설정)
        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
            name: my-scheduler-config
            namespace: kube-system
        data:
            my-scheduler-config.yaml: |
                apiVersion: kubescheduler.config.k8s.io/v1beta2
                kind: KubeSchedulerConfiguration
                profiles:
                - schedulerName: my-scheduler
                leaderElection: # 리더를 설정함으로 스케줄링을 주도할 scheduler를 선출
                    leaderElect: true
                    resourceNamespace: kube-system
                    resourceName: lock-object-my-scheduler
        ```
    1. Deployment yaml 파일 구성
        ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
            labels:
                component: scheduler
                tier: control-plane
            name: my-scheduler
            namespace: kube-system  # 해당 ns에 Deployment를 생성
        spec:
            selector:
                matchLabels:  # Deployment의 label과 맞춰준다
                    component: scheduler
                    tier: control-plane
            replicas: 1
            template:
                metadata:
                labels:
                    component: scheduler
                    tier: control-plane
                    version: second
                spec:
                    serviceAccountName: my-scheduler
                    containers:
                    - command:
                        - /usr/local/bin/kube-scheduler
                        - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
                        image: gcr.io/my-gcp-project/my-kube-scheduler:1.0
                        livenessProbe:
                        name: kube-second-scheduler
                        resources:
                            requests:
                                cpu: '0.1'
                        volumeMounts:
                        - name: config-volume
                            mountPath: /etc/kubernetes/my-scheduler
                    hostNetwork: false
                    hostPID: false
                    volumes:
                        - name: config-volume
                        configMap:
                            name: my-scheduler-config  # config-volume에 있는 my-scheduler-confg 파일을 활용
        ```
    1. Pod를 생성할 때, Scheduler를 정의
        - 제대로 생성되었는지 확인
            - `kubectl get event -o wide`
            - `kubectl logs my-scheduler -ns kube-system`
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
            name: nginx
        spec:
            container:
            - image: nginx
              name: nginx
            schedulerName: my-scheduler
        ```

#### Scheduler Profile
- Pod가 할당되는 과정과 플러그인
    1. Scheduling Queue: 우선순위에 기반함
        - PrioritySort 
    1. Filtering: Pod가 할당될 수 있는 Node를 선별
        - NodeResourcesFit 
        - NodeName: Pod에서 지정한 Node 확인
        - NodeUnschedulable: node의 Unschedulable 옵션 확인
    1. Scoring: 가중치를 계산
        - NodeResourcesFit 
        - ImgaeLocality
    1. Binding: 가중치가 가장 높은 Node를 선택
        - DefaultBinder
- 스케줄러에 다수의 프로필을 가질 수 있음
    - 스케줄러가 다수 있을 때, 프로세스(스케줄러) 간에 충돌이 일어날 수 있기 때문에 이를 방지
    ```yaml
    apiVersion: kubescheduler.config.k8s.io/v1
    kind: KubeSchedulerConfiguration
    profiles:
    - schedulerName: default-scheduler
    - schedulerName: no-scoring-scheduler
        plugins:
        preScore:
            disabled:
            - name: '*'
        score:
            disabled:
            - name: '*'
    ```
