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

#### Service Account
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