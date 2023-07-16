CKA_6_Network

# Network

## Prerequisite

### Switching Routing
- 네트워크에서 패킷이 목적지로 전달되는 경로를 변경하는 것
    - 패킷을 다른 경로로 전송하여 트래픽 로드 밸런싱, 장애 복구, 보안 및 성능 향상 등의 목적

#### Switch
- 데이터 링크 계층에서 동작하는 네트워크 장비
- 여러 개의 포트를 가지고 잇으며 이를 통해 여러 장치를 연결
    - 스위칭 테이블을 유지하여 포트와 MAC 주소의 매핑 정보를 저장

#### Router
- 모델 네트워크 계층에서 동작 
    - Switch를 연결
- IP 주소를 기반으로 패킷을 전달하는 역할
    - 서로 다른 네트워크와 연결하는 역할 (최적의 경로를 활용)
- 라우팅 과정
    1. 패킷 전달: 목적지 IP 주소를 통해서 적절한 네트워크로 전달
    1. 패킷 필터링: 네트워크 트래픽을 필터링. 불필요한 트래픽 차단
    1. 네트워크 주소 변환(NAT): 사설 IP주소와 공인 IP 주소 간의 통신을 지원
    1. 패킷 분할 및 조립: 큰 크기의 패킷을 작은 크기로 분할하고, 조합하여 전달
- 라우팅 테이블
    - 패킷을 전달할 경로를 저장한 DB
        - 라우터나 스위치와 같은 네트워크 장비에 저장
        - 네트워크 트래픽을 효율적으로 라우팅
    - Column
        - Destination IP: 목적지 IP 주소 범위
        - Next Hop: 패킷을 전달할 다음 라우터 또는 게이트웨이의 IP 주소 (인터페이스를 통해서 Next Hop으로 감)
        - Interface: 패킷을 전달할 다음 라우터 또는 게이트웨이의 인터페이스 (네트워크를 연결하는 물리적 또는 가상적인 연결점)
    - 예시
        Destination IP | Next Hop	| Interface
        --|--|--
        192.168.1.0/24	 | 10.0.0.1 |	eth0
        10.0.0.0/24	| 192.168.1.1 |	eth1
        0.0.0.0/0 |	10.0.0.2 |	eth2
- 종류
    1. 정적 라우팅
        - 관리자가 수동으로 경로를 정의
        - 목적지 IP 주소 또는 서브넷과 해당 경로를 매핑하여 패킷이 목적지로 전달되는 경로를 결정
        - 단순하고 예측 가능하지만, 네트워크 변화에 대응하기 어렵고 유연성이 제한적
    1. 동적 라우팅
        - 동적 라우팅은 네트워크 프로토콜을 사용하여 경로 정보를 자동으로 교환하고 업데이트
        - 동적 라우팅 프로토콜을 사용하여 경로 정보를 교환하고 네트워크의 변화에 따라 최적의 경로를 선택
        - 프로토콜 종류: OSPF (Open Shortest Path First), BGP (Border Gateway Protocol) 등
        - 네트워크의 유연성과 확장성을 향상시키며, 장애 복구 및 로드 밸런싱에도 유용합니다.
    1. 로드 밸런싱
        - 여러 대상 서버 또는 서비스 사이에서 트래픽을 분산
        - 서버의 가용성을 모니터링하고 필요에 따라 트래픽을 다른 서버로 전달하여 부하 분산


<img src="https://user-images.githubusercontent.com/40620421/246644470-8c9cd0f3-6776-455b-9f11-98d170493404.png" width="500">

#### Gateway
- 서로 다른 네트워크 간의 통신을 가능하게 하는 장치
    - 네트워크의 입구 역할
    - 프로토콜 변환, 주소 변환 등을 통해 다른 네트워크로 전달될 수 있도록 함
- Gateway
    - A (192.168.1.5) <> B (192.168.1.6/192.168.2.6) <> C (192.168.2.5)
    - A에서 C에 접근하려고 해도 안 됨 > B를 통해서 가야함
    - `ip route add 192.168.2.0/24 via 192.168.1.6` 명령을 통해서 게이트웨이에 등록
    - A에서 C 접근 시에 B로 갔다가 C로 가게 됨
 
### DNS (Domain Name System)
- 인터넷에서 도메인 이름과 IP 주소를 매핑하는 시스템
    - 예: www.example.com > 192.0.2.1 (반대도 가능)
    - /etc/hosts > `192.0.2.1   www.example.com`
    - /etc/resolv.conf > `nameserver 8.8.8.8` (windows의 경우, DNS서버 연결하는 설정이 있음)
- 구성 요소 (계층적 구조)
    - DNS 클라이언트
        - DNS 정보를 요청하는 시스템이며, 주로 사용자의 컴퓨터 또는 네트워크 장비
        - 도메인 이름을 IP 주소로 변환하거나 역으로 IP 주소를 도메인 이름으로 변환하기 위해 DNS 서버에 쿼리를 보냄
    - DNS 서버
        - DNS 정보를 저장하고 관리하는 서버 (서버마다 너무 많은 DNS를 가지고 있음)
        - DNS 서버는 도메인 이름과 해당 도메인의 IP 주소를 포함하는 DNS 레코드를 저장하고, 클라이언트의 DNS 쿼리에 응답
    - DNS 캐시 서버
        - DNS 캐시 서버는 이전에 요청한 DNS 정보를 일정 기간 동안 캐시에 저장하여 반복적인 쿼리의 응답 시간을 단축

### Docker Network
- 도커 컨테이너 간의 통신을 위한 가상 네트워크 환경을 구성하는 기능
    - 가상 네트워크 상에서 독립적인 IP 주소를 가지며, 도커 내부에서 자동으로 DNS 이름 해결도 제공
- 특징
    - 가상 네트워크: 각 네트워크는 독립적인 IP 주소 범위와 네트워크 설정을 가지며, 컨테이너 간의 통신 가능
    - 내부 및 외부 통신: 컨테이너는 같은 네트워크에 속한 다른 컨테이너와 내부 통신. 또한, 호스트 시스템이나 다른 네트워크에 있는 컨테이너와도 외부 통신 가능
    - 다양한 네트워크 드라이버: bridge, overlay, macvlan, host 등의 네트워크 드라이버를 제공   
- 종류
    1. bridge 네트워크
        - 기본적으로 Docker에서 사용하는 네트워크
        - 컨테이너 간에 가상의 스위치로 연결하여 통신
        - DNS 서비스를 통해 컨테이너 이름으로 서로 통신할 수 있음
    1. host 네트워크
        - 호스트의 네트워크 인터페이스를 컨테이너와 공유
        - 컨테이너는 호스트와 동일한 네트워크를 가짐
    1. overlay 네트워크
        - 여러 호스트에 걸쳐 컨테이너를 연결하는 가상의 네트워크
        - 여러 호스트에서 컨테이너 간에 통신 가능

```powershell
docker network create my-network  # bridge Network
docker run -d --name container1 --network my-network nginx
docker run -d --name container2 --network my-network nginx

docker exec container1 curl container2  # container1이 container2에게 HTTP 요청을 보낼 수 있음
```

## Cluster Node Networking
- Control plane
    Port Range | Purpose | Usde By
    --|--|--
    6443 |	Kubernetes API server |	All
    2379-2380 |	etcd server client API |	kube-apiserver, etcd
    10250 |	Kubelet API |	Self, Control plane
    10259 |	kube-scheduler |	Self
    10257 |	kube-controller-manager |	Self
- Worker node
    Port Range | Purpose | Usde By
    --|--|--
    10250 |	Kubelet API  |	Self
    30000-32767 |	NodePort Services |	All

```powershell
ip address  # node와 연관된 network 정보 
ip route  # node와 관련된 route 정보 
ssh node1 / exit  # node로 이동 / 나가기
netstat  # 네트워크 정보 확인
netstat -anp  # 모든 연결 및 소켓 정보를 숫자 형식으로 표시
netstat -nplt  # 리스닝 중인 포트와 소켓 정보를 표시
```


### CNI (Container Networking Interface)
- 컨테이너 오케스트레이션 시스템(예: Kubernetes, Docker)에서 컨테이너와 네트워크를 연결하기 위한 표준 인터페이스
    - Network를 생성하기 위해서 실행되는 프로세스는 docker, kubernetes 등 거의 모든 플랫폼에서 동일하다 > 표준 인터페이스로 구성
    - 컨테이너를 생성하고 관리하는 도구와 네트워크 플러그인 사이의 통신을 가능하게 함
- 개념
    - CNI 플러그인
        - 다양한 네트워크 플러그인을 지원
        - 각 컨테이너에 IP 주소를 할당하고 네트워크 설정을 구성하는 역할을 수행
        - 컨테이너를 가상 네트워크에 연결하거나, VLAN 태그를 설정하거나, 네트워크 정책을 적용하는 등의 작업을 수행
    - CNI 구성 파일
        - CNI 플러그인은 CNI 구성 파일을 통해 동작을 정의 (네트워크 구성과 관련된 정보가 포함)
        - 컨테이너를 생성할 때 CNI 플러그인에게 전달
    - CNI 호출
        - CNI 플러그인을 호출하여 네트워크 연결을 수행
        - 컨테이너 생성 시, 해당 컨테이너에 대한 CNI 구성 파일이 CNI 플러그인에 전달되고, 플러그인은 네트워크 설정을 적용합니다.

#### CNI weave
- 컨테이너 네트워크 인터페이스 (CNI) 플러그인으로 사용되는 솔루션 중 하나
    - 기존에 IP 주소를 통한 통신을 비효율적
        - Pod > Node > Service > Node > Pod
    - IP가 중복되지 않도록 테이블을 통한 관리
        ```
        IP	Status	Pod
        10.32.0.1	Assigned	pod-1
        10.32.0.2	Assigned	pod-2
        10.32.0.3	Available	-
        10.32.0.4	Assigned	pod-3
        10.32.0.5	Available	-
        ```
- 특징
    - 가상 네트워크
        - Kubernetes 클러스터 내의 모든 노드와 컨테이너 간의 통신을 위한 네트워크를 구성
        - 이 가상 네트워크는 컨테이너에게 고유한 IP 주소를 할당하고, 서로 간의 통신을 가능
    - 네트워크 보안
        - 가상 네트워크에서 암호화를 지원
    - 네트워크 연결성
        - Kubernetes 클러스터 내에서 여러 노드 간의 연결성을 제공
        - 다른 노드에 위치한 컨테이너 간의 통신은 Weave를 통해 라우팅되며, 클러스터의 크기에 관계없이 네트워크 연결성을 확보
    - 스케일링 및 확장성
        - 새로운 노드가 추가되거나 노드가 제거될 때 Weave는 네트워크 설정을 자동으로 조정하여 새로운 노드와 기존 노드 간의 연결을 유지
    - 간편한 배포
        - Kubernetes 클러스터의 각 노드에 Weave 플러그인을 설치하면 자동으로 네트워크가 구성되며, 컨테이너 간의 통신이 가능
- 컨테이너 간 통신
    1. 클러스터 내 컨테이너 생성
        - 클러스터에는 여러 노드(Node1, Node2, Node3)가 존재
        - 각 노드에는 여러 개의 컨테이너(Pod)가 실행 중
        - Weave는 각 노드에 설치되어 컨테이너 간의 네트워크 통신을 관리
    1. Weave 가상 네트워크 구성
        - Weave는 각 노드에 가상 네트워크 인터페이스를 생성
        - 각 컨테이너는 가상 네트워크 인터페이스에 연결되며, 고유한 IP 주소를 할당 받음
    1. 컨테이너 간 통신
        - 컨테이너 A가 컨테이너 B에게 통신을 요청
        - Weave는 송신 컨테이너 A의 IP 주소와 포트 정보를 확인
        - Weave는 목적지 컨테이너 B의 IP 주소를 알고 있으므로 통신을 위해 패킷을 전달
        - 패킷은 Weave의 가상 네트워크를 통해 목적지 컨테이너 B에 도달
        - 목적지 컨테이너 B는 패킷을 받아들이고, 필요한 작업을 수행한 후 응답을 송신 컨테이너 A에게 반환


### Pod Networking
- Pod 간에 네트워킹은 어려움
    - Pod 내에서 인스턴스 간에 네트워킹
    - Pod와 Pod 간에 네트워킹
    - 다른 노드의 Pod와 Pod에 대한 네트워킹 
        - 다른 노드에 네트워킹하기 위해서는 Gateway를 거쳐가야 함
        - (노드1에서 노드2의 Pod 네트워킹) > (노드2의 Gate way) > (노드2의 Pod)
- 주요 기능
    - IP 주소
        - 각 Pod는 고유한 IP 주소를 가지며, Pod 내의 모든 컨테이너는 동일한 IP 주소를 공유
    - 동일한 호스트 내 통신
        - 동일한 노드(호스트)에 있는 Pod는 동일한 호스트 네트워크 인터페이스를 공유 > 로컬 네트워크를 통해 직접 통신
        - 네트워크 오버헤드를 최소화하고 빠른 통신을 구현
    - Pod 간 통신
        - 다른 노드에 있는 Pod와도 통신 가능
        - 네트워크 트래픽을 라우팅하고 로드 밸런싱하기 위한 가상 네트워크를 제공
    - Service
        - Kubernetes에서는 Service를 통해 Pod에 대한 안정적인 네트워크 주소와 로드 밸런싱을 제공
        - 여러 Pod를 그룹화하여 단일 Endpoint를 제공하고, 클라이언트에서는 Service의 IP 주소를 통해 Pod에 접근

### Service Networking
- Service는 실존하는 Resource가 아닌 가상 객체
    - CPU, MEM을 사용하지도 않음
    - Node에 종속되지도 않음
- Service 생성
    - 각 Node의 Kube-proxy에 Forwarding rule을 저장한다
    ```
    {IP Address}:{port} | Forward To
    서비스 IP | 서비스에 속한 Pod의 IP
    ```
    - 서비스 IP를 요청받으면 Pod로 네트워킹 됨

### DNS in Kubernetes
- DNS 이름을 IP 주소로 해석하고, 서비스 및 Pod 간의 통신을 가능하게 함
    - 서비스 및 Pod를 식별
    - 애플리케이션은 동적인 환경에서 서비스 디스커버리와 로드 밸런싱을 수행
    - 기본적으로 DNS가 구성됨
    - Service DNS Example: `<service-name>.<namespace>.svc.cluster.local`
- DNS는 서버로 따로 관리됨 (CoreDNS)
    - 만약 각자 컨테이너가 가지고 있다면 비효율적으로 중복되는 값이 들어감
    - 각 컨테이너는 DNS 서버 정보만 가지고 있어도 됨
    - kube-system namespace에 존재함
- 임의로 설정 가능 (spec>containers>args)
    ```
    apiVersion: v1
    kind: Pod
    metadata:
        name: my-pod
    spec:
    containers:
        - name: my-container
        image: my-image
        ports:
            - containerPort: 8080
        command: ["my-application"]
        args: ["--service-host=my-service.default.svc.cluster.local"]
    ```
- 테이블
    ```
    +---------------------+-------------------+-------------------+-------------------+-------------------+
    |       DNS Name      |    Namespace      |     IP Address     |   Service Type    |     Annotations    |
    +---------------------+-------------------+-------------------+-------------------+-------------------+
    |  my-service         |     default       |  10.32.0.5         |   ClusterIP       |    -               |
    |  my-service         |     namespace1    |  10.32.0.10        |   ClusterIP       |    -               |
    |  my-service         |     namespace2    |  10.32.0.15        |   ClusterIP       |    -               |
    |  backend-service    |     default       |  10.32.0.20        |   ClusterIP       |    -               |
    |  frontend-service   |     default       |  10.32.0.25        |   ClusterIP       |    -               |
    |  my-pod             |     default       |  10.32.1.5         |   Pod             |    app=my-app      |
    |  your-pod           |     default       |  10.32.1.10        |   Pod             |    app=your-app    |
    +---------------------+-------------------+-------------------+-------------------+-------------------+
    ```

### Ingress
- 클러스터 내부 또는 외부에서 애플리케이션에 접근하기 위한 진입점을 제공하는 리소스
    - IP:port로 접근하는 방식에서 벗어남
    - HTTP 및 HTTPS 트래픽을 관리하며, 클라이언트의 요청을 서비스로 전달하는 역할을 수행
    - 트래픽의 분산, 가상 호스트, 경로 기반 라우팅, SSL/TLS 암호화 등 다양한 기능을 제공
- 특징
    - 외부 노출
        - Ingress는 클러스터 외부에 있는 로드 밸런서나 인그레스 컨트롤러를 통해 클러스터 내부의 서비스를 외부로 노출
        - 클라이언트는 특정 도메인 이름 또는 경로를 사용하여 서비스에 접근
    - 가상 호스트
        - Ingress는 가상 호스트를 지원하여 동일한 IP 주소 및 포트에서 다수의 도메인 이름을 사용하여 다른 서비스에 접근
        - 예를 들어, example.com 및 api.example.com 도메인을 사용하여 각각 다른 서비스에 접근
    - 경로 기반 라우팅
        - Ingress는 경로 기반 라우팅을 지원하여 특정 URL 경로를 사용하여 서비스로 요청을 전달
        - 이를 통해 서로 다른 경로에 대해 다른 서비스로 트래픽을 분배
    - SSL/TLS 지원
        - SSL/TLS 암호화를 지원하여 애플리케이션의 보안을 강화
        - 인그레스 컨트롤러가 SSL/TLS 인증서를 관리하고 암호화된 트래픽을 업스트림 서비스로 전달
- 예시
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
        name: my-ingress
    spec:
    rules:
        - host: example.com
        http:
            paths:
            - path: /service1  # example/service1은 service1로 전달
                pathType: Prefix
                backend:
                service:
                    name: service1
                    port:
                    number: 80
            - path: /service2  # example/service2는 service2로 전달
                pathType: Prefix
                backend:
                service:
                    name: service2
                    port:
                    number: 80
    ```
- 클라이언트 요청 처리 순서
    ```
        +--------------+
        |   Ingress    |
        +--------------+
                |
                |
                v
        +--------------+
        | Load Balancer|
        +--------------+
                |
                |
                v
        +--------------+
        |  Kube Proxy  |
        +--------------+
                |
                |
                v
        +--------------+
        |   Pod(s)     |
        +--------------+

    ```