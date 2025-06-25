CKA_7_Security

# Security
- 클러스터의 안정성과 데이터의 기밀성, 무결성, 가용성을 보장하기 위해 중요한 측면
- 보안은 지속적으로 강화되어야 하며, Kubernetes의 새로운 보안 기능과 업데이트를 주시적으로 적용하여 보안 취약점으로부터 클러스터를 보호하는 것이 중요
- kube-apiserver에서 1차적 security 진행
    - Who can access?
        - Username and PW/Tokens
        - Certificates
        - External Authentication Providers - LDAP
        - Service Accounts
    - What can they do?
        - RBAC Authorization
        - ABAC Authorization
        - Node Autorization
- TLS Certification
    - Kube-system (ETCD, kubelet 등) 간에 보안을 담당


### Authentication
- 사용자나 리소스가 클러스터에 접근할 때 신원을 확인하는 과정
    - 클러스터의 보안을 강화하고, 불법적인 접근을 차단하며, 사용자나 리소스에 대한 권한 부여를 제어하는데 중요한 역할
    - 모든 사용자는 kube-api-server를 통해서 k8s에 접근하는데, 이때 인증 절차를 거침
        - kube-api-server에 토큰 정보를 전달
- 주요 인증 매커니즘
    - 사용자 이름과 비밀번호(Basic Authentication)
        - 사용자가 아이디와 비밀번호를 사용하여 인증
        - 비밀번호를 평문으로 전송하기 때문에 보안에 취약하므로 보통 추천되지 않음
    - 클라이언트 인증서(Client Certificates)
        - 클라이언트가 Kubernetes API 서버에 접근할 때, 클라이언트 인증서를 사용하여 인증
        - 클라이언트는 사전에 발급된 인증서와 개인 키를 사용하여 자신을 인증
    - 서비스 계정(Service Accounts)
        - Kubernetes 클러스터 내의 Pod들은 서비스 계정을 사용하여 API 서버에 접근
        - 서비스 계정은 네임스페이스 내에서 생성되며, 특정 작업을 수행하는데 필요한 권한을 가지고 있음
    - OAuth 토큰 인증
        - OAuth를 사용하여 사용자나 클라이언트 애플리케이션을 인증하는 방법
- 예시
    - User(Admins, Developers), Service Accounts(Bots)

### Service Account
- 클러스터 내에서 실행되는 Pod나 애플리케이션에게 인증 정보를 제공하는 리소스
    - 필요한 권한을 가진 Service Account를 생성하여 애플리케이션에게만 필요한 권한을 제한적으로 부여
    - 사용자 계정과는 달리 자동으로 생성되며, 네임스페이스별로 관리
    - API 토큰 (API Token), Secret으로 구성
- 사용 예시
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: my-pod
    spec:
    serviceAccountName: my-service-account
    containers:
        - name: my-container
            image: nginx
    ```

## TLS Certificates

### TLS 인증서란?
- **TLS(Transport Layer Security) 인증서**는 인터넷상에서 서버와 클라이언트 간의 안전한 통신을 보장하는 디지털 인증서
- SSL(Secure Sockets Layer)의 후속 프로토콜인 TLS는 보다 강화된 보안 기능을 제공하며, 현재 웹 보안의 핵심 기반
- 브라우저의 주소창에 표시되는 자물쇠 아이콘과 HTTPS 프로토콜이 바로 TLS 인증서가 적용된 결과

### TLS의 핵심 기능
#### 암호화 (Encryption)
- 브라우저와 웹 서버 간 전송되는 모든 데이터를 암호화하여 제3자의 스니핑(도청)으로부터 보호합니다
- 대칭키와 비대칭키 암호화를 조합하여 효율적이면서도 안전한 통신을 구현합니다

#### 인증 (Authentication)
- 서버의 신원을 확인하여 클라이언트가 신뢰할 수 있는 서버와 통신하고 있음을 보장합니다
- CA(Certificate Authority)에서 발급한 인증서를 통해 도메인 소유권과 조직의 실체를 검증합니다

#### 무결성 (Integrity)
- 전송 중인 데이터가 위조되거나 변조되지 않았음을 확인합니다
- 메시지 인증 코드(MAC)와 디지털 서명을 통해 데이터의 무결성을 보장합니다

### TLS Handshake 과정
#### 1. Client Hello
- 클라이언트가 지원하는 TLS 버전, 암호화 방식(Cipher Suite), 클라이언트 랜덤 데이터를 서버에 전송

#### 2. Server Hello
- 서버는 TLS 인증서와 공개키를 보낸다

#### 3. 인증서 검증
- 클라이언트가 서버 인증서의 유효성을 확인
    - CA의 공개키로 인증서 서명을 검증하여 서버의 신원 확인
- 만약 이 과정을 거치지 않으면 해커가 만든 임의의 웹사이트가 정당한지 확인할 수 없음

#### 4. 키 교환
- 클라이언트가 세션키(대칭키)를 생성하여 서버의 공개키로 암호화하여 전송

#### 5. 암호화 통신 시작
- 양측이 동일한 세션키를 보유하게 되면 암호화된 통신 개시
- 공개키로 암호화된 세션키는 서버의 개인키로만 해독할 수 있음


### CA(Certificate Authority)와 인증서 체인

#### CA의 역할
- **인증 기관(CA)**은 디지털 인증서를 발급하고 관리하는 신뢰할 수 있는 제3자 기관
- CA는 자체적으로 공개키와 비밀키를 보유하며, 이 비밀키로 인증서에 디지털 서명을 함

#### 인증서 체인 구조
1. **Root CA**: 최상위 인증기관으로 자체 서명된 인증서 보유
2. **Intermediate CA**: Root CA로부터 인증받아 실제 인증서 발급
3. **End Entity**: 최종 사용자나 기업이 받는 인증서

이러한 계층 구조를 **Chain of Trust(신뢰 체인)**라고 하며, 상위 기관의 공개키로 하위 기관을 검증하는 방식으로 보안을 보장합니다.

### public key / private key
- 공개키: 누구에게나 공개되는 키로, 데이터 암호화나 서명 검증에 사용
    - *.crt, *.pem
- 개인키: 소유자만 알고 있는 비밀 키로, 복호화나 디지털 서명 생성에 사용
    - *.key, *-key.pem
- 한 키로 암호화하면 반드시 쌍을 이루는 다른 키로만 복호화 가능


## TLS in Kubernetes
- Kubernetes 클러스터의 모든 컴포넌트는 HTTPS 기반의 암호화된 통신으로 이루어집니다. 
    - kube-apiserver ↔ etcd: kube-apiserver는 etcd에 접근할 수 있는 유일한 컴포넌트로, 클러스터 상태를 Key-Value 형식으로 저장합니다
    - kubelet ↔ kube-apiserver: Worker 노드의 kubelet이 Pod 정보를 가져오고 노드 상태를 전달합니다
    - kubectl ↔ kube-apiserver: 사용자 명령과 Manifest 파일을 JSON 형식으로 전달합니다
    - kube-scheduler ↔ kube-apiserver: 스케줄되지 않은 Pod에 노드를 배정합니다
    - kube-controller-manager ↔ kube-apiserver: 클러스터 오브젝트의 상태를 관리합니다

### PKI 인증서 체계
Kubernetes는 **독자적인 PKI(Public Key Infrastructure)**를 구성하여 인증서를 관리합니다. 

#### Kubernetes CA
- 위치: /etc/kubernetes/pki/ca.crt, /etc/kubernetes/pki/ca.key
- 역할: etcd를 제외한 모든 컴포넌트의 인증서 서명에 사용
- Common Name: 'KUBERNETES-CA'

#### ETCD CA
- 위치: /etc/kubernetes/pki/etcd/ca.crt, /etc/kubernetes/pki/etcd/ca.key
- 역할: ETCD 전용 인증서 서명 및 관리
- 이유: ETCD는 별도의 CA를 사용하여 보안을 강화합니다

### 인증서 분류

#### Server 인증서
- kube-apiserver: 클라이언트의 요청을 받는 서버 역할
- kubelet: Worker 노드에서 서버 역할 수행
- etcd: 클러스터 상태 저장소 서버 역할

#### Client 인증서
- kubectl: 사용자 명령을 전달하는 클라이언트
- kube-scheduler: API 서버의 클라이언트로 작동
- kube-controller-manager: API 서버와 통신하는 클라이언트
- kube-proxy: 서비스 정보를 가져오는 클라이언트
- kube-apiserver는 특별히 두 개의 클라이언트 인증서를 보유합니다
    - apiserver-kubelet-client.crt: Kubernetes CA에서 발급
    - apiserver-etcd-client.crt: ETCD CA에서 발급
