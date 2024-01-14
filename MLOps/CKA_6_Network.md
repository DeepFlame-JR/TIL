CKA_6_Network

# Network

## Prerequisite
 
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
    ```
    Port Range | Purpose                   | Used By
    --------------------------------------------------
    6443       | Kubernetes API server     | All
    2379-2380  | etcd server client API    | kube-apiserver, etcd
    10250      | Kubelet API               | Self, Control plane
    10259      | kube-scheduler            | Self
    10257      | kube-controller-manager   | Self
    ```

- Worker node
    ```
    Port Range | Purpose            | Used By
    --------------------------------------------------
    10250      | Kubelet API        | Self
    30000-32767| NodePort Services  | All
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
    - 각 노드에 `peer`라는 에이전트 배포
        - 클러스터 내의 POD 및 IP 정보를 알고있음
        - 각 노드에 Weave Bridge를 구축하고, IP 주소를 할당
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
    - 네트워크 연결성
        - Kubernetes 클러스터 내에서 여러 노드 간의 연결성을 제공
        - 다른 노드에 위치한 컨테이너 간의 통신은 Weave를 통해 라우팅되며, 클러스터의 크기에 관계없이 네트워크 연결성을 확보
            - weave 범위는 `weave POD`, `ip route` 등을 통해서 확인가능
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
        - Weave는 송신 컨테이너 A의 IP 주소와 포트 정보를 확인 & 목적지 컨테이너 B의 IP 주소를 알고 있으므로 통신을 위해 패킷을 전달
        - 패킷은 Weave의 가상 네트워크를 통해 목적지 컨테이너 B에 도달
        - 목적지 컨테이너 B는 패킷을 받아들이고, 필요한 작업을 수행한 후 응답을 송신 컨테이너 A에게 반환


### Pod Networking
- 특징
    - 각 Pod는 고유한 IP 주소를 가지며, Pod 내의 모든 컨테이너는 동일한 IP 주소를 공유
    - Service
        - Kubernetes에서는 Service를 통해 Pod에 대한 안정적인 네트워크 주소와 로드 밸런싱을 제공
        - 여러 Pod를 그룹화하여 단일 Endpoint를 제공하고, 클라이언트에서는 Service의 IP 주소를 통해 Pod에 접근

1. Container to Container
    - 여러 개의 컨데이너를 하나의 가상 네트워크 인터페이스에 할당
    - 외부에서 컨테이너를 구별할 때는 port 번호로 구분
    - pause 컨테이너가 각 Pod마다 존재하며 다른 컨테이너들에게 네트워크 인터페이스를 제공하는 역할

<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*LWnaWtGo_OYilqKPZ_Zk4Q.png">

2. Pod to Pod
    - Pod 네트워킹 인터페이스로 CNI 스펙을 준수하는 네트워크 플러그인 사용
    - Pod는 고유한 IP를 가지고 있기 때문에 서로 통신 가능함
    - 만약 다른 노드에 있다면 Router를 거쳐서 다른 노드로 이동 

<img src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*bum5JtYR0YLGdnWjWCNv3Q.png">

3. Pod to Service
    - Pod는 쉽게 대체될 수 있는 존재이기 때문에 Pod to Pod 네트워크는 내구성이 약함


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
        - `<service-name>.<namespace>`
        - `<service-name>.<namespace>.svc`
        - `<service-name>.<namespace>.svc.cluster.local`
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