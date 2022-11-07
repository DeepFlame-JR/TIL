3_Scheduling

# Scheduling

### Manual Schedule
- 스케줄러는 생성된 Pod를 사용가능한 Worker Node에 할당	
    - 스케줄러가 제대로 동작하지 않는다면 Node가 할당되지 않음
- yaml 파일에서는 NodeName 값을 설정하지 않으면 Binding 객체를 만들어 할당한다	
    - 만약 NodeName을 지정하면 해당 노드에 할당됨 `spec > nodeName`
    - Binding 객체  {바인딩 yaml 파일 붙이기}

### Label & Selector
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

#### Node Selector
- 특정 Pod가 특정 Node에서만 실행될 수 있도록 설정
    - 리소스를 많이 점유하는 Pod를 리소스가 많은 Node에서 실행하도록 한다
- 설정
    - node에 label 설정 `kubectl label nodes node-1 size=Lareg`
    ```yaml
    spec:
        nodeSelector:
            size: Large
    ```