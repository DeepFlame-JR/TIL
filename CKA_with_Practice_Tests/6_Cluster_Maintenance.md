6_Cluster_Maintenance

# Cluster Maintenance

### OS 업그레이드
- `k drain node-1`: 노드를 의도적으로 비움
    - replica가 있는 경우, 컨테이너들은 다른 노드로 이동
    - replica가 없는 경우, 해당 명령을 활용할 수 없음 > deployment로 변경 후, 진행
- `k cordon node-2`: 노드를 예약 불가능의 상태로 만듬
- `k uncordon node-1`: 노드를 재부팅
    - 이후 새로운 Pod를 받게 됨

### K8S Upgrade
- `k get nodes` >> K8S Version이 나타남
- kube API 서버가 가장 핵심적인 요소 > 다른 구성 요소들은 더 낮은 버전이어야 함
- 최근 3개의 버전만 support함
    - 1.10(설치), 1.11, 1.12 출시된 상황에서 1.13 출시 전에 업그레이드를 해줘야함
    - 1.10 > 1.13 바로 업그레이드가 아니라 한 단계씩 업그레이드 진행
- 방법
    - Master Node > Worker Node 업그레이드
    - Master Node가 업그레이드 되는 동안 API 서버, Control Plane 등의 주요 요소 들이 다운됨
        - Worker Node에서는 Pod를 새로 배포하는 등의 작업을 할 수 없음
        - 그러나, 기존에 존재하던 애플리케이션들은 정상적으로 동작
    - Worker Node 업그레이드 방법은 다수가 있음
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
