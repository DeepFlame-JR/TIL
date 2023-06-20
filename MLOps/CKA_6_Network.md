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


#### CNI (Container Networking Interface)
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
    {IP Address}:{port} | Forward To
    -- | --
    서비스 IP | 서비스에 속한 Pod의 IP
    - 서비스 IP를 요청받으면 Pod로 네트워킹 됨