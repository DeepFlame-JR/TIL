CKA_4_Operation_in_Kubernetes

# Kubernetes 운영

### Deployment
- 애플리케이션을 배포/업데이트하는데 사용되는 리소스
- Rolling Update, Rollout, Rollback은 업데이트 전략

#### Rolling Update
- 기존의 Pod를 새로운 버전의 Pod로 교체하는 방식으로 업데이트 진행
- 업데이트 과정에서 오류가 발생하면 Rollback이 가능
- 과정
    ReplicaSet v1 | v1_Pod1 | v1_Pod2 | ReplicaSet v2 | v2_Pod1 | v2_Pod2
    --|--|--|--|--|--
    Ready | Ready | Ready | Pending | Pending | -
    Ready | Terminating | Ready | Pending | Ready | -
    Ready | Terminating | Ready | Pending | Ready | Pending
    Terminating | Terminating | Terminating | Ready | Ready | Ready
- 명령어
    - `kubectl set image deployment <deployment-name> <container-name>=<new-image>`

#### Rollout
- 업데이트 상태를 관리
    - 업데이트는 여러 단계를 거치며, Rollout은 이러한 단계를 추적하고 관리
    - 업데이트를 일시 중지하거나 다시 시작하는 등의 제어
    - 업데이트 전략과 관련된 파라미터를 설정하여 업데이트 방식을 조정 (업데이트 전략, 롤백 정책, 병렬 업데이트 수 등)
- 명령어
    - `kubectl rollout <command> deployment <deployment-name>`
        - command=status: 업데이트 진행률, 사용 가능한 Pod 수, 배포된 Pod의 상태 등 확인
        - command=pause: 롤아웃을 일시 중지
        - command=resume: 중지된 롤아웃을 다시 시작
        - command=history: Deployment의 업데이트 기록을 확인 (이전에 수행된 롤아웃 이벤트 포함)

#### Roleback
- 이전 상태의 애플리케이션으로 복원
    - Deployment는 이전 버전의 ReplicaSet을 보존하고 있어서 가능
- 명령어
    - `kubectl rollout undo deployment <deployment-name> --to-revision=<revision>`
        - --to-revision: Optional, 롤백할 리비전 지정 가능


### Application Commands
- Pod이 시작될 때 컨테이너 내에서 실행될 명령어를 정의
    - 컨테이너가 시작될 때 필요한 초기화 작업이나 실행할 명령어를 명시적으로 정의
        - 예) 데이터베이스 초기화 스크립트를 실행
    - 웹 서버, DB 또는 단순한 계산 등의 작업을 진행하고, 완료되면 컨테이너를 종료하는 경우가 있음
- 예시
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: my-pod
    spec:
        containers:
            - name: my-container
            image: my-image
            command: ["python", "app.py"]
    ```

## 환경변수 제어

### 1. Contianer 정의에서 설정
```yaml
spec:
  containers:
    - name: my-container
      image: my-image
      env:
        - name: ENV_VAR1
          value: value1
        - name: ENV_VAR2
          value: value2
```

### 2. ConfigMap
- 애플리케이션의 환경 데이터를 관리
- 애플리케이션의 설정 변경 없이 구성 값을 업데이트하거나 재사용 가능

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  ENV_VAR1: value1
  ENV_VAR2: value2
---
spec:
  containers:
    - name: my-container
      image: my-image
      envFrom:
        - configMapRef:
            name: my-configmap
```

### 3. Secrets
- 민감한 정보를 저장하는 데에 사용 (DB 계정, 비밀번호 등)
    - etcd에서는 암호화가 진행되지 않음 (암호화 구성 사용 권장)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
data:
  ENV_VAR1: dmFsdWUx  # base64로 인코딩된 값
  ENV_VAR2: dmFsdWUy
---
spec:
  containers:
    - name: my-container
      image: my-image
      envFrom:
        - secretRef:
            name: my-secret
```


## Cluster 관리
### OS 업그레이드
1. `k drain node-1`: 노드를 의도적으로 비움
    - replica가 있는 경우, 컨테이너들은 다른 노드로 이동
    - replica가 없는 경우, 해당 명령을 활용할 수 없음 > deployment로 변경 후, 진행
1. `k cordon node-2`: 노드를 예약 불가능의 상태로 만듬
1. `k uncordon node-1`: 노드를 재부팅
    - 이후 새로운 Pod를 받게 됨

### Kubernetes Upgrade
- `k get nodes` >> K8S Version이 나타남
- kube API 서버가 가장 핵심적인 요소 > 다른 구성 요소들은 더 낮은 버전이어야 함
- 최근 3개의 버전만 support함
    - 1.10(설치), 1.11, 1.12 출시된 상황에서 1.13 출시 전에 업그레이드를 해줘야함
    - 1.10 > 1.13 바로 업그레이드가 아니라 한 단계씩 업그레이드 진행
- 방법
    1. Master Node > Worker Node 업그레이드
    1. Master Node가 업그레이드 되는 동안 API 서버, Control Plane 등의 주요 요소 들이 다운됨
        - Worker Node에서는 Pod를 새로 배포하는 등의 작업을 할 수 없음
        - 그러나, 기존에 존재하던 애플리케이션들은 정상적으로 동작
    1. Worker Node 업그레이드 방법은 다수가 있음
        1. 모두 한번에 업그레이드
            - 사용자가 애플리케이션에 접근할 수 없음
        1. 한번에 하나의 노드 업그레이드
            - 노드가 중지된 동안 컨테이너들은 다른 노드로 이동됨
        1. 업그레이드된 새 노드를 추가
            - 클라우드 환경이라면 새 노드를 쉽게 프로비저닝할 수 있기 때문에 용이함
- 명령어
    ```powershell
    k drain node-1

    apt-get upgrade -y kubeadm=1.12.0-00
    apt-get upgrade -y kubelet=1.12.0-00

    kubeadm upgrade node config --kubelet-version v1.12.0
    systemctl restart kubelet

    k uncordon node-1
    ```

### 백업 및 복원
1. Resource Configuration
    - 쉽게 정의할 수 있고, 공유하기도 쉬움
        - GitHub를 활용하여 팀단위로 관리 가능
        - 클러스터가 손실되더라도 복구할 수 있음
    - 명령어
        ```powershell
        k create namespace new-namespace
        k create configmap

        k apply -f pod-definition.yaml
        
        # 모든 구성 요소를 가져옴
        k get all --all-namespaces -o yaml > all-deploy-services.yaml
        ```

        - `k create namespace new-namespaces` `k create configmap`
        - `k apply -f pod-definition.yaml`
        - `k get all --all-namespaces -o yaml > all-deploy-services.yaml`: 모든 구성요소를 가져옴
1. ETCD Cluster
    - 클러스터 상태를 저장함 > 서버 자체를 백업할 수 있음
        - 노드 및 모든 리소스를 저장
    - `etcd.service` 의 --data-dir에 백업할 디렉토리를 설정할 수 있음
    - etcd DB의 스냅샷을 만들 수 있음
        - 저장할 때 보안을 위해 인증서 파일을 지정하도록 권장
        - `ETCDCTL_API=3 etcdctl snapshot save snapshot.db`
        - `ETCDCTL_API=3 etcdctl snapshot status snapshot.db`
            - `export ETCDCTL_API=3`를 통해서 앞에 부분을 생략할 수 있음
    - 복원
        ``` powershell
        service kube-apiserver stop 

        ETCDCTL_API=3 etcdctl snapshot restore snapshot.db
        --data-dir /var/lib/etcd-from-backup

        systemctl daemon-reload
        service kube-apiserver start
        ```
