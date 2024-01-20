KServe

# Kserve
- Knative의 구성 요소 중 Knative Serving과 유사한 목표를 가지고 있지만 별도로 개발됨
    - 점점 복잡해지는 모델 서빙을 위한 기능들이 추가됨
    - Knative Serving을 기반으로 하면서도 Knative 외부에서도 사용 가능한 추가적인 확장성과 기능을 제공
- ML/DL 모델을 실제로 서비스하기 위해 API를 쉽게 만들 수 있도록 도와주는 툴

<img src="https://private-user-images.githubusercontent.com/40620421/296244429-59525bb4-c193-4d1c-9219-4c259688a5cc.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNjM5ODAsIm5iZiI6MTcwNTA2MzY4MCwicGF0aCI6Ii80MDYyMDQyMS8yOTYyNDQ0MjktNTk1MjViYjQtYzE5My00ZDFjLTkyMTktNGMyNTk2ODhhNWNjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDEyNDgwMFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTkzYzI0NWU3YWFlZjM0MmZlMWMxMmQxZDc2Zjc3ZDNjNDg1OWViMjIzMGU0ZmExMjYxNGJhNzRiN2Y0NWI5ZWImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.b-OPEI39q6fCxkevKjUoA091J930aor6Ik4jIWyNBK8">

### 장점
- Kubernetes 위에서 구동하기 때문에 확장성과 강력한 관리 기능을 활용할 수 있음
- 쉽고 간편하게 Model에 대한 API를 제공할 수 있음
- Zero Scale이 가능해서 Pod가 없다가 호출을 받으면 띄워서 isvc가 수행될 수 있음
- 버전을 올리면서 canary 배포가 가능함 > 이를 통해서 자연스럽게 버전업이 가능

### 동작 원리
- 생성 과정
    1. (InitContainer) storage-initializer가 storageUri 주소에서 모델을 Volume으로 다운로드함
    1. Transformer에서의 요청을 받아 처리한 후 다시 Transformer Pod로 보냄
    1. (Container) Kserve-container에서 모델 배포를 위한 코드가 실행됨, 실행 내용은 servingRuntime에 정의됨
    1. (Sidecar) queue-proxy는 동시에 처리가능한 요청의 수를 관리하여, 다수의 요청을 안정적으로 관리하는 역할
- isvc 생성시 아래 리소스들이 생성됨
    - Service: Knative Service는 컨테이너 이미지 및 배포 구성과 전반적인 구성을 정의
        - Route, Configuration, Revision, Podautoscaler
    - virtualSerivce, service, deployment
- 요청이 들어오는 순서
```
  +----------------------+        +-----------------------+      +--------------------------+
  |Istio Virtual Service |        |Istio Virtual Service  |      | K8S Service              |
  |                      |        |                       |      |                          |
  |sklearn-iris          |        |sklearn-iris-predictor |      | sklearn-iris-predictor   |
  |                      +------->|-default               +----->| -default-$revision       |
  |                      |        |                       |      |                          |
  |KServe Route          |        |Knative Route          |      | Knative Revision Service |
  +----------------------+        +-----------------------+      +------------+-------------+
   Knative Ingress Gateway           Knative Local Gateway                    Kube Proxy
   (Istio gateway)                   (Istio gateway)                          |
                                                                              |
                                                                              |
  +-------------------------------------------------------+                   |
  |  Knative Revision Pod                                 |                   |
  |                                                       |                   |
  |  +-------------------+      +-----------------+       |                   |
  |  |                   |      |                 |       |                   |
  |  |kserve-container   |<-----+ Queue Proxy     |       |<------------------+
  |  |                   |      |                 |       |
  |  +-------------------+      +--------------^--+       |
  |                                            |          |
  +-----------------------^-------------------------------+
                          | scale deployment   |
                 +--------+--------+           | pull metrics
                 |  Knative        |           |
                 |  Autoscaler     |-----------
                 |  KPA/HPA        |
                 +-----------------+
```

1. Gateway
```yaml
kind: Gateway
metadata:
  labels:
    networking.knative.dev/ingress-provider: istio
    serving.knative.dev/release: v0.12.1
  name: knative-ingress-gateway
  namespace: knative-serving
spec:
  selector:
    istio: ingressgateway  # Ingress에서 오는 요청을 받아옴
  servers:
  - hosts:
    - '*'
    port:
      name: http
      number: 80
      protocol: HTTP
  - hosts:
    - '*'
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      mode: SIMPLE
      privateKey: /etc/istio/ingressgateway-certs/tls.key
      serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
```

2. sklearn-iris VirtualService
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: sklearn-iris
  namespace: default
  gateways:  # 해당 gateway의 요청을 가져옴
  - knative-serving/knative-local-gateway
  - knative-serving/knative-ingress-gateway
  hosts:  # host가 가 아래인 것이 대상
  - sklearn-iris.default.svc.cluster.local
  - sklearn-iris.default.example.com
  http:
  - headers: # 요청의 헤더를 설정
      request:
        set:
          Host: sklearn-iris-predictor-default.default.svc.cluster.local  
    match:
    - authority:
        regex: ^sklearn-iris\.default(\.svc(\.cluster\.local)?)?(?::\d{1,5})?$
      gateways:
      - knative-serving/knative-local-gateway
    - authority:
        regex: ^sklearn-iris\.default\.example\.com(?::\d{1,5})?$
      gateways:
      - knative-serving/knative-ingress-gateway
    route:
    - destination:
        host: knative-local-gateway.istio-system.svc.cluster.local  # local-gateway로 보냄
        port:
          number: 80
      weight: 100
```

3. sklearn-iris-predictor-default-mesh VirtualService
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: sklearn-iris-predictor-default-mesh
  namespace: default
spec:
  gateways:
  - knative-serving/knative-ingress-gateway
  - knative-serving/knative-local-gateway
  hosts:
  - sklearn-iris-predictor-default.default
  - sklearn-iris-predictor-default.default.example.com
  - sklearn-iris-predictor-default.default.svc
  - sklearn-iris-predictor-default.default.svc.cluster.local
  http:
  - match:
    - authority:
        prefix: sklearn-iris-predictor-default.default
      gateways:
      - knative-serving/knative-local-gateway
    - authority:
        prefix: sklearn-iris-predictor-default.default.svc
      gateways:
      - knative-serving/knative-local-gateway
    - authority:
        prefix: sklearn-iris-predictor-default.default
      gateways:
      - knative-serving/knative-local-gateway
    retries: {}
    route:
    - destination:
        host: sklearn-iris-predictor-default-00001.default.svc.cluster.local
        port:
          number: 80
      headers:
        request:
          set:
            Knative-Serving-Namespace: default
            Knative-Serving-Revision: sklearn-iris-predictor-default-00001
      weight: 100
  - match:
    - authority:
        prefix: sklearn-iris-predictor-default.default.example.com
      gateways:
      - knative-serving/knative-ingress-gateway
    retries: {}
    route:
    - destination:
        host: sklearn-iris-predictor-default-00001.default.svc.cluster.local
        port:
          number: 80
      headers:
        request:
          set:
            Knative-Serving-Namespace: default
            Knative-Serving-Revision: sklearn-iris-predictor-default-00001
      weight: 100
```

4. Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: sklearn-iris-predictor-default-fhmjk-private
  namespace: default
spec:
  clusterIP: 10.105.186.18
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 8012  # Inference pod의 8012 포트로 연결
  - name: queue-metrics
    port: 9090
    protocol: TCP
    targetPort: queue-metrics
  - name: http-usermetric
    port: 9091
    protocol: TCP
    targetPort: http-usermetric
  - name: http-queueadm
    port: 8022
    protocol: TCP
    targetPort: 8022
  selector:
    serving.knative.dev/revisionUID: a8f1eafc-3c64-4930-9a01-359f3235333a
  sessionAffinity: None
  type: ClusterIP
```

5. Queue Proxy
6. Kserve container



# K-native
- kubernetes 위에서 서버리스 애플리케이션을 구축, 배포 및 실행하기 위한 플랫폼

#### 서버리스
- 서버리스 프로비저닝, 관리, 확장 등의 작업이 자동으로 이루어짐
- 서버를 직접 관리하지 안항 신경 쓸 필요가 없는 경우
- 기존에 온프레미스나 클라우드의 경우, 사용하지 않아도 시간마다 돈이 소요됨
    - 필요한 컴퓨팅 리소스와 스토리지만 동적으로 할당하고 그 부분만 비용을 청구

### 구성 요소
- Knative Serving
    - 서버리스 애플리케이션의 배포와 관리를 담당하는 구성 요소
    - 컨테이너화된 애플리케이션을 더 쉽게 배포하고, 버전 관리, 자동 스케일링, 트래픽 라우팅 등의 기능을 제공
- Knative Eventing
    - 이벤트 기반 아키텍처를 구현하는 데 사용되는 구성 요소
    - 이벤트 소스로부터 이벤트를 수집하고, 이를 처리하는 애플리케이션을 실행
- Knative Build
    - 소스 코드를 컨테이너 이미지로 변환하고 레지스트리에 푸시하는 등의 작업을 자동화
    - 지속적인 통합 및 배포 (CI/CD) 시나리오를 지원


# Istio
- MSA(MicroService Architecture)의 분산 네트워크 환경에서 각 app들의 네트워크를 쉽게 설정할 수 있음
- 이벤트 메쉬를 구현할 수 있는 오픈 소스 솔루션
    - 서비스 코드 변경 없이 로드밸런싱, 서비스 간에 인증, 모니터링 등 MicroService를 쉽게 관리할 수 있음
- MicroService 간의 모든 통신을 담당할 수 있는 프록시인 Envoy를 사이드 패턴으로 MS에 모두 배포
    - 프록시 들의 설정값 저장 및 관리/감독 수행 및 설정값을 전달하는 컨트롤러 역할 수행
    - Data Plane: 사이드카 패턴으로 배포된 Envoy 프록시
    - Control Plane: Data Plane을 컨트롤 하는 부분

#### 서비스 메쉬
- API등을 사용하여 Micro Service 간 통신을 안전하고, 빠르고, 신뢰할 수 있게 만들기 위해 설계된 전용 인프라 계층
- Application 서비스에 경량회 Proxy를 sidecar 방식으로 배치하여 서비스 간 통신을 제어
    - MSA에서는 데이터를 하나의 인스턴스에서 진행하던 작업을 나누어서 하기때문에 서비스 간 통신이 원활하게 진행되어야 할 필요가 있음
- 로드 밸런싱, 서비스 디스커버리, 보안, 로깅 등의 기능을 제공
- 기존 방식을 적용한다면?
    - MSA 시스템에서 수많은 서비스&인스턴스들이 동작하기 때문에 관리가 매우 복잡 
        - 외부에서 접근하기 위해서는 서비스 하나하나 포트 포워딩을 해야함
    - 기존에 프로세스나 쓰레드 간 메모리 공유 등으로 처리했던 기능들이 서비스 간 통신을 통해 처리됨 > 어려움
    - 구성 관리, 로그 처리 등을 런타임마다 구현해야함으로 MSA지만 조잡하고, 둔해짐


<img src="https://private-user-images.githubusercontent.com/40620421/296547288-24a697f9-ff58-4677-bfda-7c6363d88384.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUyMzI4NDUsIm5iZiI6MTcwNTIzMjU0NSwicGF0aCI6Ii80MDYyMDQyMS8yOTY1NDcyODgtMjRhNjk3ZjktZmY1OC00Njc3LWJmZGEtN2M2MzYzZDg4Mzg0LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTQlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTE0VDExNDIyNVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTgwODgwMTk4ZTYwYWEyMzQxODY1YTQ5ZWE5MzZjZjg2ZGZiNGYwN2RiZDBhOGU2NDBmOTU0ZWViZDJlNjExY2UmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.rzOvWXGlqKFEKZnUjVB7qitIEJ9IG2vDkcqUx_vZYMU">


### 핵심 구성
1. Data Plane
    - Service의 Sidecar 형태로 구성된 Proxy를 가리킴
        - Istio에서는 envoy를 Proxy로 활용함 (Retry, Timeout 등의 기능을 지원)
    - MicroService에 들어오고 나가는 모든 트래픽을 통제
1. Control Plane
    - Envoy를 컨트롤하는 부분
    - 구성
        - Mixer
            - 서비스 메쉬 전체 영역에서 Access 제어 및 정책 관리
            - Envoy와 여러 서비스에서 모니터링 지표 수집
        - Pilot
            - Envoy 설정 관리를 수행
            - Envoy가 호출하는 서비스의 주소를 얻을 수 있는 서비스 디스커버리 기능 제공
            - 서버 트래픽 라우팅 기능 제공
            - 호출 재시도, 타임아웃 등의 기능 제공
        - Citadel
            - 보안 관련 기능을 수행
            - 사용자 인증/인가, TLS(SSL)을 이용한 통신 암호화 및 인증서 관리
        - Galley
            - Istio의 구성 및 설정 검정, 배포 관리

### 기능
1. 트래픽 통제
    - 트래픽 분할: 서로 다른 버전의 서비스를 배포해두고, 버전별로 트래픽 양을 조절할 수 있음
    - 컨텐츠 기반 분할: 네트워크 패킷의 내용으로 라우팅 가능
1. 서비스간 안정성 제공 (Resilience)
    - 파일럿은 대상 서비스가 여러 개의 인스턴스로 구성되어 있으면 이를 로드밸런싱하고, 장애가 난 서비스가 있으면 자동으로 제거
    - 서비스 간의 호출 안정성을 위해서 재시도 횟수 통제 가능
1. 보안
    - 기본적으로 envoy를 통해서 통신하는 모든 트래픽을 자동으로 TLS를 이용해서 암호화
        - 이는 인증서를 사용하는 데 Citadel에 저장된 인증서를 다운받아 사용
    - 서비스 인증/인가
    - 서비스 간에도 인증서를 이용하여 양방향 TLS 인증 진행
    - 사용자에게는 JWT 토큰을 이용하여 서버에 접근할 수 있는 클라이언트를 인증
    - RBAC을 통해서 롤을 부여하여 서비스 접근 권한을 관리
1. 모니터링
    - MSA에서는 서비스 간에 호출되는 의존성을 알기 어려움
    - 네트워크 트래픽을 모니터링 하면서 서비스의 응답 시간, 처리량 등의 다양한 지표를 수집
    - 예시
        - 서비스 A가 B를 호출할 때, 호출 트래픽은 각각의 envoy 프록시를 통하게 되고 응답 시간, 처리량이 Mixer에 전달됨


#### Envoy
- MSA 단일 Application과 Service를 위해 설계된 고성능 분산 C++ 프록시
- 디자인 목표
    - 모듈화가 잘 되어 있으며 테스트가 용이하도록
    - 플랫폼에 독립적
- 주요 기능
    - 모든 프로그래밍 언어, 프레임워크와 함께 실행될 수 있음
    - (L3/L4 Architecture) TCP 프록시, HTTP 프록시, TLS 인증과 같은 다양한 작업 진행
    - (L7 Architecture) 버퍼링, 속도 제한, 라우팅/전달 등의 작업 수행
    - 자동 재시도, Circuit break, 이상치 탐지 등의 기능 제공
        - Circuit break: 오류가 발생하면 더이상 동작하지 않게 하여 리소스 무한 점유를 막음


### 트래픽 관리
- Istio에서는 `gateway`, `virtualService`, `destinationRule`을 통해서 트래픽을 관리

#### Gateway
- Istio 서비스 메쉬에 유입되는 트래픽의 관문
- 클러스터 외부에서 서비스에 접근할 수 있도록 구성

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: my-gateway
spec:
  selector:
    istio: ingressgateway # Istio IngressGateway에 연결
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
```

#### VirtualService
- 트래픽을 적절히 라우팅하는 istio crd
- 실제로 존재하지 않지만 마치 존재하는 것처럼 동작하는 가상의 엔드포인트
    - 실제 Pod로 실행되지 않고 ingressgateway의 envoy 설정을 변경

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: virtual-service
spec:
  hosts:
  - "*"  # 모든 호스트의 트래픽을 받음
  gateways:
  - gateway-name
  http:  # Match에 따라서 서비스를 라우팅
  - match:
    - uri:
        prefix: "/service-a"  # Uri 확인
    - headers:
        end-user:
            exact: user-a  # header 확인
    route:
    - destination:
        host: service-a
        port:
          number: 80
  - match:
    - uri:
        prefix: "/service-b"
    route:
    - destination:
        host: service-b
        port:
          number: 80
      weight: 20  # 서비스 분배 가능 (Canary)
    - destination:
        host: reviews.prod.svc.cluster.local
        subset: v1
      weight: 80
```


#### DestinationRule
- 트래픽을 어떻게 보낼지에 대한 정의
- 트래픽의 로드 밸런싱, 품질 관리, 시간 초과 및 재시도 제어 등을 구체적으로 설정 가능

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-a
spec:
  host: service-a  # 대상 host
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN  # 가장 연결이 적은 엔드포인트로 트래픽을 전송
  connectionPool:
    tcp:
      maxConnections: 100  # TCP 연결의 최대 수를 100으로 설정
    http:
      http1MaxPendingRequests: 50  # HTTP/1.1 최대 대기 요청 수를 50으로 설정
      http2MaxRequests: 100  # HTTP/2의 최대 대기 요청 수를 100으로 설정
      maxRequestsPerConnection: 5  # 하나의 연결당 최대 요청 수를 5로 설정
  outlierDetection:
    consecutiveErrors: 5  # 연속적으로 발생한 오류가 5회 이상이면 OutlierDetection 동작
    interval: 1s  # 주기 1초
    baseEjectionTime: 30s  # Outlier 표시된 후, 30초 간 트래픽 제외
    maxEjectionPercent: 10  # 최대 10% 엔드포인트가 Outlier로 표시될 수 있음
```

#### 예시
1. 로드밸런서로 트래픽이 유입
1. ingressgateway controller는 실제 pod를 실행
    - pod 들의 설정은 Gateway, VirtualService에 의해서 configuration이 결정됨
1. 이것만으로 트래픽이 연결되지만, 트래픽 연결 정책을 추가하고 싶은 경우 DestinationRule을 지정

<img src="https://private-user-images.githubusercontent.com/40620421/296556793-7609836a-6c14-4e44-9b10-75ce445e1056.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUyNDMzMzksIm5iZiI6MTcwNTI0MzAzOSwicGF0aCI6Ii80MDYyMDQyMS8yOTY1NTY3OTMtNzYwOTgzNmEtNmMxNC00ZTQ0LTliMTAtNzVjZTQ0NWUxMDU2LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTQlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTE0VDE0MzcxOVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTE0MzMwYzg4NTNhMGI4ODNjM2UyZWRiMjIxYWJlMzcwM2RlNzljYjI2YTA4ODc2MjhlMDU4MDAxNDA3YjU3MGYmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.AwfPMWTefHICxRQvroDU1FAN8B1GLPXoW46Xishr6sc">
출처: https://findstar.pe.kr/2021/04/14/istio-traffic-management/

