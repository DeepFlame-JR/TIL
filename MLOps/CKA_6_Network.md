CKA_6_Network

# Network

## Docker Network (선수지식)
- 도커는 가상 네트워크 환경을 구성함
    - 각 컨테이는 내부 IP를 순차적으로 할당받음
    - 이는 재시작할 때마다 변경될 수 있음
    - 컨테이너는 같은 네트워크에 속한 다른 컨테이너와 내부 통신 가능
    - 호스트 시스템이나 다른 네트워크에 컨테이너와도 외부 통신 가능
- 종류
    1. bridge 네트워크
        - 기본적으로 Docker에서 사용하는 네트워크
        - 컨테이너 간에 가상의 스위치로 연결하여 통신
        - DNS 서비스를 통해 컨테이너 이름으로 서로 통신할 수 있음
        - 컨테이너를 생성하면 디폴트로 bridge 네트워크로 연결됨
    1. host 네트워크
        - 호스트의 네트워크 인터페이스를 컨테이너와 공유
        - 호스트의 네트워크를 사용하기 때문에 포트포워딩이 필요 없음
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
https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model

<img src="https://kubernetes.io/docs/images/kubernetes-cluster-network.svg">

- 특징
    - 각 Pod는 고유한 IP 주소를 가지며, Pod 내의 모든 컨테이너는 동일한 IP 주소를 공유
    - Service
        - Kubernetes에서는 Service를 통해 Pod에 대한 안정적인 네트워크 주소와 로드 밸런싱을 제공
        - 여러 Pod를 그룹화하여 단일 Endpoint를 제공하고, 클라이언트에서는 Service의 IP 주소를 통해 Pod에 접근

1. Container to Container
    - 여러 개의 컨데이너를 하나의 가상 네트워크 인터페이스에 할당
    - 외부에서 컨테이너를 구별할 때는 port 번호로 구분
    - pause 컨테이너가 각 Pod마다 존재하며 다른 컨테이너들에게 네트워크 인터페이스를 제공하는 역할
2. Pod to Pod
    - Pod 네트워킹 인터페이스로 CNI 스펙을 준수하는 네트워크 플러그인 사용
    - Pod는 고유한 IP를 가지고 있기 때문에 서로 통신 가능함
    - 만약 다른 노드에 있다면 Router를 거쳐서 다른 노드로 이동 
3. Pod to Service
    - Pod는 쉽게 대체될 수 있는 존재이기 때문에 Pod to Pod 네트워크는 내구성이 약함


### Service Networking
https://kubernetes.io/docs/concepts/services-networking/service/
- Service는 실존하는 Resource가 아닌 가상 객체
    - CPU, MEM을 사용하지도 않음
    - Node에 종속되지도 않음
- Pod 네트워크 접근성을 제공하기 위함 (안정성과 가용성을 향상)
    - 클러스터 외부로부터 요청을 받을 수 있도록 함
    - 동일한 레이블 셀렉터를 가진 Pod들의 그룹을 캡슐화하여 추상화 (단일 지점으로 많은 Pod를 동작시킬 수 있음)
- 종류는 ClusterIP > NodePort > LoadBalancer가 있는데, 모두 중첩기능으로 설계되어, 각 단계에 더해지는 형태
- Service 생성
    - 각 Node의 Kube-proxy에 Forwarding rule을 저장한다
    ```
    {IP Address}:{port} | Forward To
    서비스 IP | 서비스에 속한 Pod의 IP
    ```
    - 서비스 IP를 요청받으면 Pod로 네트워킹 됨
- 주요 기능
    - 고정된 IP 주소 (DNS 시스템 / etcd를 통해서 도메인 관리)
    - 로드 밸런싱 (여러 개 파드가 있는 경우, 요청을 분산)
    - 서비스 디스커버리 (DNS 이름 또는 IP 주소를 사용하여 애플리케이션 파드 접근)

#### ClusterIP
- 가장 기본이 되는 Service 타입, 클러스터 내부 통신만 가능
- Service가 관리하는 Pod들에게 로드밸런싱

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app.kubernetes.io/name: proxy
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
    - containerPort: 8080  # 내부에서 사용하는 포트
      name: http-web-svc

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:  # label이 'app.kubernetes.io/name: proxy'를 가진 Pod를 대상으로함
    app.kubernetes.io/name: proxy
  ports:
  - name: name-of-service-port
    protocol: TCP
    port: 80  # 외부에서 Service에 접근할 때 사용되는 포트 번호
    targetPort: 8080  # Pod 내부의 포트
    targetPort: http-web-svc  # 이름으로도 연결 가능
```

- 다른 네임스페이스 또는 클러스터에 존재하는 Pod와 네트워크 연결을 위해 서비스-서비스 간의 연결을 해야하는 상황도 있음
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376

---
apiVersion: v1
kind: Endpoints
metadata:
  name: myapp-service  # 연결할 서비스와 동일한 name을 메타데이터로 입력
subsets:  # 해당 서비스로 가리킬 endpoint를 명시
  - addresses:
      - ip: 192.0.2.42
    ports:
      - port: 9376
```


#### NodePort
- 클러스터 내부 및 외부 통신이 가능
- 노드 포트를 사용함 (30000-32767)
    - 모든 노드의 특정 포트를 사용함

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - port: 80  # 서비스 노출 포드
      targetPort: 80  # Pod에서 노출하는 포트
      # 선택적 필드 (명시하지 않은 경우 사용 가능한 포트를 자동으로 할당)
      # 기본적으로 그리고 편의상 쿠버네티스 컨트롤 플레인은 포트 범위에서 할당한다(기본값: 30000-32767)
      nodePort: 30007  # 만약 30007 포트가 사용 중이라면 실패
```

#### LoadBalancer
- 서비스를 생성함과 동시에 로드밸런서를 새롭게 생성해 Pod와 연결
    - LoadBalancer > Service > Pod
    - LoadBalancer > Random Node > 해당 노드 or 다른 노드의 Pod
- 기본적으로 외부에 존재하여 외부 트래픽을 받는 역할
    - 외부에 존재하기 때문에 클라우드 사용시에 가격 부담이 있을 수 있음
- 도메인 이름과 IP를 할당받아, 좀 더 쉽게 Pod에 접근할 수 있음
    - 도메인 이름이 설정되어야 함 (온프레미스에서는 별도 작업 필요)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80				# 서비스를 노출하는 포트
      targetPort: 80		# 애플리케이션(파드)를 노출하는 포트
  clusterIP: 10.0.171.239	# 클러스터 IP
  selector:
    app: myapp
    type: frontend
status:
  loadBalancer:				# 프로비저닝된 로드 밸런서 정보
    ingress:
    - ip: 192.0.2.127
```

#### ExternalName
- 클러스터 내부에서 외부 도메인 이름을 사용하기 위해 사용
- 클러스터에서 `my-external-service`를 사용하여 외부 도메인에 접근할 수 있음
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  type: ExternalName
  externalName: example.com
```

### Ingress
- 하나의 URL을 통해서 트래픽을 받고, 정의한 규칙에 따라 적절한 pod에 전달됨
    - 만약 Ingress가 정의 되지 않는다면, 각 deployment에 서비스를 하나씩 연결해야 함
    - IP:port로 접근하는 방식에서 벗어남
    - 트래픽의 분산, 가상 호스트, 경로 기반 라우팅, SSL/TLS 암호화 등 다양한 기능을 제공
- 예시
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      annotations:
        # Ingress로 들어온 요청의 경로를 백엔드 서비스에 보낼 때 /로 바꿔서 보내라
        # Service로 보낼 때 항상 / 경로 처리하기 위해서 사용
        nginx.ingress.kubernetes.io/rewrite-target: /
        nginx.ingress.kubernetes.io/ssl-redirect: "false"
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
        | Load Balancer| <외부
        +--------------+
                |
    ------------|------------
                v
        +--------------+
        |   Ingress    | <내부
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

#### Ingress Controller
- 쿠버네티스에서 공식적으로 개발하는 Nginx 웹 서버 인그레스 컨트롤러
    - 이 외에도 Kong, GKE 등의 솔루션이 있음
- Nginx 인그레스 컨트롤러를 구성하면 자동으로 LoadBalancer 타입의 서비스가 생성됨


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


### CNI (Container Networking Interface)
https://kubernetes.io/docs/concepts/cluster-administration/addons/
- 컨테이너 오케스트레이션 시스템(예: Kubernetes, Docker)에서 컨테이너간의 네트워크를 연결하기 위한 표준 인터페이스
    - 컨테이너 런타임과 오케스트레이터 사이의 네트워크 계층을 구현하는 방식이 다양하게 분리되어 각자의 방식으로 발전하는 것을 방지
    - K8s에서는 Pod 간의 통신을 위해 CNI 사용
        - kubenet이라는 자체적인 CNI가 제공되지만, 3rd-party 플러그인이 주로 사용됨
    - 컨테이너를 생성하고 관리하는 도구와 네트워크 플러그인 사이의 통신을 가능하게 함
- CNI의 필요성
    1. 파드 간에 통신을 하기 위해서는 네트워크 주소와 게이트웨이 주소 간의 매핑 관계를 routing table로 일일히 정의해야함
    1. UI Container > Login Container로 요청을 보냄
        - K8s는 멀티 호스트로 구성되어 있어 둘 다 172.17.0.2 
        - 이런 컨테이너끼리 통신하기 위해 반드시 CNI가 설치되어야 함
        - CNI가 브릿지 인터페이스를 만들고 컨테이너 네트워크 대역대를 나누어 주며, 라우팅 테이블까지 생성 

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