Helm
참고: https://kubetm.github.io/helm/

# Helm
- Kubernetes의 package manager
- 도입 배경
    - kubectl 명령을 통해서 App을 생성/배포
    - 각 App 마다 yaml 파일 생성
    - 각 배포 환경마다 yaml 파일이 생성됨 >> 관리하는 yaml 파일이 점점 어려움
- yaml 파일을 동적으로 관리
    - yaml 파일은 정적 파일이라서 리소스 별로 yaml 파일을 만들어야 함
    - 많은 리소스 관리하기에 유지보수가 어려움
    - 하나의 template을 통해서 yaml 파일을 동적으로 생성하게 함
    - 변수를 줌으로써 yaml 파일을 추가하는 과정을 줄임
- v3 버전은 client에서 kubernetes cluster의 API Server에 직접적으로 통신함
- Helm을 지원하는 Opensource들이 많음
    - 사용자 환경에 따라서 옵션 값을 변경하여 활용할 수 있음

#### Getting started
```bash
# repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list
helm search repo bitnami | grep tomcat
helm repo update
helm repo remove bitnami

# 배포
helm install my-tomcat bitnami/tomcat --version 10.5.17 --set persistence.enabled=false

helm list
helm status my-tomcat
helm uninstall my-tomcat
kubectl get pods

# 다운로드
helm pull bitnami/tomcat --version 10.5.17
tar -xf ./tomcat-10.5.17.tgz
helm install my-tomcat . --set persistence.enabled=false
```
    