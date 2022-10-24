CKA_with_Practice_Tests

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