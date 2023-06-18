CKA_3_Scheduling

# Scheduling
- 노드에 Pod를 할당하는 프로세스
- 실행
    1. 노드 선택: 조건에 따라 노드를 필터링, 특정 노드에 이미 Pod가 많으면 노드를 제외할 수 도 있음
    1. 리소스 할당: 각 노드의 가용한 리소스와 Pod의 요구 사항을 비교
    1. 노드 평가: 스케줄러는 후보 노드를 평가하여 얼마나 적합한지를 결정
    1. 우선순위 설정: 각 후보 노드에 대해 우선순위를 설정하여 가장 적합한 노드를 선택. (우선순위는 리소스 사용률, Pod 간의 접근성, Pod 간의 약정, 노드의 가용성 등을 고려)
    1. 할당 및 배치: 가장 높은 우선순위를 가진 후보 노드에서 실행될 수 있도록 스케줄러가 배치.


## Manual Schedule
- 사용자가 직접 특정 노드에 할당할 수 있음
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeName: my-node
  containers:
  - name: my-container
    image: nginx
```

### Taints & Tolerations
- Node에 Pod를 배치하는 전략
    - 어떤 Pod가 어떤 Node에 배치되는지, 제한 되는지에 관함
- `Taint`
    - Node에 설정함으로 원치않는 Pod가 할당되는 것을 방지
        - Master Node의 경우, Pod를 예약하지 않도록 Taints가 설정되어 있음
        - 노드 보호: 특정 노드에 다른 Pod가 스케줄링되지 않도록 할 수 있음 > 안정적인 노드 유지  
        (GPU Node에는 GPU가 있는 Pod만 올라갈 수 있도록 함)
        - Pod 제한: 특정 Pod가 특정 노드에만 실행할 수 있도록 제한할 수 있음
    
    - `kubectl taint nodes node-1 key=value:effect`
        - node-1에서 key=value인 Pod만 할당 가능
        - effect
            - NoSchedule: key-value에 맞지 않는 Pod를 스케줄하지 않음
            - preferNoSchedule: 스케줄을 하지 않는 것이 선호되지만, 보장하지 않음
            - NoExecute: 새 포드가 노드에 생성되지 않음, 만약 기존에 있다면 제거됨
- `Toleration`
    - Pod에 설정함으로써 Taints를 우회하고, 특정 노드에 Pod를 스케줄링할 수 있음
    - Toleration이 설정되었다고 해당 Taint가 있는 노드에 반드시 할당되는 것은 아님
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: my-pod
    spec:
        containers:
        - name: my-container
            image: nginx
        tolerations:
        - key: "key"
          operator: "Equal" # Equal, Exists 등
          value: "value"
          effect: "NoSchedule"
    ```

### NodeSelector
- 특정 Pod가 특정 Node에서만 실행될 수 있도록 쉽게 설정
    - 리소스를 많이 점유하는 Pod를 리소스가 많은 Node에서 실행하도록 함
- 설정
    - node에 label 설정 `kubectl label nodes node-1 size=Large`
    ```yaml
    spec:
        nodeSelector:
            size: Large
    ```

### Affinity
- Pod가 특정 노드와의 관계를 설정하는 방법
- 종류
    1. Node Affinity: Pod가 특정 노드에 스케줄링되거나 특정 노드에 대한 제약 조건을 설정
    1. Pod Affinity: Pod가 다른 Pod와 함께 배치되도록 할 수 있으며, 특정 Pod와 동일한 노드에 스케줄링될 수 있도록 지정

#### Node Affinity
- Pod가 특정 노드에서 호스팅되도록 설정
    - Node Selector에 비해 고급 기능 지원 / 복잡
    ```yaml
    spec.affinity.nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
                # Size 값이 Large 또는 Medium인 Node에 할당
                - key: size
                    operator: In  # NotIn, Exists 등
                    values:
                    - Large
                    - Medium
    ```
- Node Affinity Types: Pod의 타입 수명 주기 단계를 설정
    - DuringScheduling: 스케줄링 중에 Pod에 영향을 줄 것인가
    - DuringExecution: 실행 중에 Pod에 영향을 줄 것인가
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

#### Taints & Tolerations & NodeAffinity
원하는 Pod를 Node에 할당시키고 싶다!
1. Taints & Tolerations만 설정?
    - Node 입장에서 받고 싶은 Pod를 정의
    - 일반 Pod > 할당받지 못 함
    - Torleration > 다른 Node에 할당될 수도 있음
1. Node Affinity만 설정?
    - Pod 입장에서 할당 되고 싶은 Node를 정의
    - 일반 Pod가 Node에 할당될 수도 있음
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
