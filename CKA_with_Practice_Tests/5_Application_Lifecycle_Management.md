5_Application_Lifecycle_Management

# Application Lifecycle Management


### Deployment
- 애플리케이션을 업그레이드 해보자!
    - Deploy 생성 > Rollout 트리거링 > 새 버전(Revision) 생성
    - 롤백도 가능
- 명령어
    - `kubectl rollout status deployment/myapp-deployment` >> 롤아웃 진행
    - `kubectl rollout undo deployment/myapp-deployment` >> 이전 버전을 가져옴
    - `kubectl rollout history deployment/myapp-deployment` >> 롤아웃 기록 확인
- 배포 전략
    1. 재생성 전략
        - 기존의 인스턴스를 모두 제거 > 새로운 인스턴스 생성
        - 모두 제거된 동안 사용자가 인스턴스를 활용할 수 없는 단점
    1. 롤링 업데이트
        - 하나의 인스턴스를 제거 > 새로운 인스턴스 > 반복
        - 기본 배포 전략
- kubectl apply
    - `kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1` > 이미지 변경

### Application Commands
- 컨테이너는 VM과 다르게 OS를 호스팅하기 위한 것이 아님
    - 프로세스가 있는 동안만 살아있음
    - 웹 서버, DB 또는 단순한 계산 등의 작업을 진행하고, 완료되면 컨테이너를 종료
    - Nginx, Mysql 등의 Dockerfile은 CMD ["nginx"] / CMD ["mysqld"] 등으로 끝남
- `docker run ubuntu` 를 했을 때, container가 제대로 실행되지 않음
    - Ubuntu의 경우 CMD ["bash"] 로 구성되어있고, 이는 프로세스가 아님
    - 그럼 어떻게?
        ```powershell
        CMD ["sleep", "5"]  # Dockerfile에 추가
        docker build -t ubuntu-sleeper . 
        docker run ubuntu-sleeper
        docker run ubuntu-sleeper sleep 10  # CMD 명령어가 모두 바뀜

        ENTRYPOINT ["sleep"]  # Dockerfile에 추가
        CMD ["5"]  # 기본 값 설정(옵션)
        docker run ubuntu-sleeper 10  # sleep 명령은 그대로고 매개변수만 전달됨
        docker run --entrypoint sleep2.0 ubuntu-sleeper 10 # sleep 명령어도 변경 가능
        ```

### K8S 환경 변수 설정
- env 속성 아래 정의
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: envar-demo
    labels:
        purpose: demonstrate-envars
    spec:
    containers:
    - name: envar-demo-container
      image: gcr.io/google-samples/node-hello:1.0
      env:
      - name: APP_COLOR
          value: blue
      - name: APP_MODE
          value: prod
    ```
- ConfigMap 활용
    - env 속성이 많아지면 관리가 어려움
    - 중앙에 ConfigMap을 구성하고 관리
    - ConfigMap 생성
        ```bash
        # 방법 1
        kubectl create configmap \
            config-name --from-literal=APP_COLOR=blue
            config-name --from-literal=APP_MODE=prod

        # 방법 2
        kubectl create configmap \
            config-name --from-file=app_config.properties
        ```
        ```yaml
        APP_COLOR: blue
        APP_MODE: prod
        ```
    - Pod에 적용
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
            name: envar-demo
        labels:
            purpose: demonstrate-envars
        spec:
        containers:
        - name: envar-demo-container
          image: gcr.io/google-samples/node-hello:1.0
          envFrom:
          - configMapRef:
              name: config-name
          - configMap Ref:  # 하나만 가져올 수 있음
              name: config-many-things
              key: one-of-them
          volumes:
          - name: app-config-volume
            configMap:
              name: app-config
        ```