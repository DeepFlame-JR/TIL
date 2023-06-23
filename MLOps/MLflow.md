MLflow

# MLflow
- ML의 생명 주기를 관리
    - 모델 개발과 훈련, 추적, 관리 및 배포를 위한 도구를 제공
    - 모델 개발 프로세스를 효율적으로 관리
- 기존의 문제점
    - 실험을 결과를 관리하고, 추적하기 어려움
    - 코드를 온전히 저장하고(environment 포함), 재생성하기 어려움
    - model의 중앙 저장소가 없음
- 구성 요소
    - Tracking
        - 모델 개발 과정에서 실험과 결과를 추적하고 관리
        - 하이퍼파라미터, 메트릭, 로그 등을 기록하고 웹 기반 대시보드에서 시각화
    - Projects
        - 모델 개발을 위한 코드, 환경 및 종속성을 패키징하고 실행하는 기능을 제공
        - Reproducible(재현 가능한) 환경을 구성하여 프로젝트를 다른 환경에서도 쉽게 실행할 수있도록 함
    - Models
        - 다양한 모델 포맷과 배포 형식을 지원하며, 모델 버전 관리와 모델 서빙을 통해 모델을 배포하고 활용
        - TensorFlow, PyTorch, Scikit-learn 등 다양한 프레임워크와 통합
    - Registry
        - 모델의 메타데이터, 버전 관리, 모델 용량 관리 등을 지원
        - 여러 사용자가 함께 작업하고 모델을 추적하며, 배포할 수 있는 환경을 제공
- 유즈 케이스
    - DS: 실험의 매개변수와 메트릭을 기록 > UI에서 결과를 비교 > 결과를 MLflow 모델로 저장
    - MLOps Engineer: 모델 성능을 비교하고, 배포를 위해 최상의 모델 선택 > MLflow Registry에 등록 > Production 환경에 배포

## Tracking
- 모델 개발 과정에서 실험의 매개변수, 메트릭, 로그 등을 기록하고 관리하는 도구
    - 웹 기반 대시보드를 제공하여 실험과 결과를 시각화
    - 실험의 매개변수, 입력데이터, 환경설정 등을 기록하여 재현 가능성을 보장
- 구현 시나리오
    1. MLflow on localhost
        - local 파일 시스템을 활용 (./mlruns)
        - FileStore, LocalArtifactRepository 정의
    1. MLflow on localhost with SQLite
        - SQLite를 활용 > mlruns.db.
        - SQLAlchemyStore, LocalArtifactRepository 정의
    1. MLflow on localhost with Tracking Server
        - Tracking Server를 생성 > backend-store-uri를 설정할 수 있음 > Server에 내용 저장
        - Artifacts는 localhost에서 정의 (LocalArtifactRepository)
    1. MLflow with remote Tracking Server, backend and artifact stores
        - Tracking Server > backend-store-uri > remote DB
        - Artifacts > Tracking Server에서 주소를 알려줌 > localhost에서 boto3 Credential을 통해 S3에 저장
    1. MLflow Tracking Server enabled with proxied artifact storage access
        - 프록시를 통해 artifacts storage에 접근할 수 있는 기능을 활성화
            - Tracking server에 접근할 수 있는 유저는 artifacts storage도 접근할 수 있음
            - 접근 권한이 있다고 가정하기 때문에 MLflow를 사용하는 대상이 모두 조회해도 되는지에 대한 주의 필요
        - RestStore(Localhost) > SQLAlchemyStore(Server) > remote DB
        - HttpArtifactRepository(Localhost) > Server > S3
    
## Projects
- 환경 설정 및 종속성을 관리하고, 코드 실행을 쉽게 재현하고 공유할 수 있는 기능
    - 코드와 환경의 일관성, 독립적인 실행 환경, 코드를 쉽게 재현 + 공유와 협업


```yaml
name: my_project

# 환경 설정에 대한 정보를 줌
python_env: python_env.yaml  # python 환경 정보를 줌
# or
# conda_env: my_env.yaml
# or
# docker_env:  # Docker로 실행할 때는 컨테이너가 생성됨
#    image:  mlflow-docker-example

entry_points:
  main:
    parameters:
      data_file: path
      regularization: {type: float, default: 0.1}
    command: "python train.py -r {regularization} {data_file}"
  validate:
    parameters:
      data_file: path
    command: "python validate.py {data_file}"

---
python: "3.8.15"
build_dependencies:
  - pip
  - setuptools
  - wheel==0.37.1
dependencies:
  - mlflow==2.3
  - scikit-learn==1.0.2
```

```py
import sys
import argparse
from sklearn.linear_model import LinearRegression

def train(alpha):
    ...

    model = LinearRegression(alpha=alpha)
    model.fit(X, y)
    mlflow.sklearn.log_model(model, "model")

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--alpha", type=float, default=0.5, help="Regularization parameter")
    args = parser.parse_args()

    train(args.alpha)

# 실행: mlflow run my_project --backend kubernetes --backend-config examples/docker/kubernetes_config.json
```

## Models


## Model Registry
- 모델 버전, 아티팩트 관리 및 배포를 간편하게 할 수 있음
- 모델에 대한 CRUD 제공
    ```py
    with mlflow.start_run() as run:
        X, y = make_regression(n_features=4, n_informative=2, random_state=0, shuffle=False)
        X_train, X_test, y_train, y_test = train_test_split(
            X, y, test_size=0.2, random_state=42
        )
        params = {"max_depth": 2, "random_state": 42}
        model = RandomForestRegressor(**params)
        model.fit(X_train, y_train)

        # Infer the model signature
        y_pred = model.predict(X_test)
        signature = infer_signature(X_test, y_pred)

        # Log parameters and metrics using the MLflow APIs
        mlflow.log_params(params)
        mlflow.log_metrics({"mse": mean_squared_error(y_test, y_pred)})

        # 모델 저장
        mlflow.sklearn.log_model(
            sk_model=model,
            artifact_path="sklearn-model",
            signature=signature,
            registered_model_name="sk-learn-random-forest-reg-model",
        )

        result = mlflow.register_model(
            "runs:/d16076a3ec534311817565e6527539c0/sklearn-model", "sk-learn-random-forest-reg"
        )

        client = MlflowClient()
        result = client.create_model_version(
            name="sk-learn-random-forest-reg-model",
            source="mlruns/0/d16076a3ec534311817565e6527539c0/artifacts/sklearn-model",
            run_id="d16076a3ec534311817565e6527539c0",
        )

        # 모델 로딩
        loaded_model = mlflow.sklearn.load_model("sklearn-model")
        predictions = loaded_model.predict(X_test)

        model_name = "sk-learn-random-forest-reg-model"
        stage = "Staging"
        model = mlflow.pyfunc.load_model(model_uri=f"models:/{model_name}/{stage}")
        model.predict(data)

    ```


#### Kubeflow & MLflow 
- 차이점
    - Kubeflow
        - 훈련 파이프라인을 통해서 워크플로우를 구축하고 실행하기 위한 플랫폼
        - 머신 러닝 워크로드를 배포하고 실행하는 데 중점
        - 분산학습과 모델 서빙을 관리
        - Kubernetes 기반의 컨테이너화된 환경을 사용
    - MLflow
        - 중앙 집중식으로 실험 추적, 모델 버전 관리, 모델 등록 등을 통합 관리
            - 팀 협업과 모델 공유를 용이
- Kubeflow & MLflow
    - Kubeflow로 모델 훈련을 수행한 후, MLflow로 저장된 모델을 다른 환경에서 로드하여 추론을 수행
        - Kubeflow Component 간에는 Json 형태로 전달이 가능 > 복잡한 형태는 Path를 활용
        - Run마다 다른 Path에 값을 저장하기 위해서는 Run마다 다른 Parameter 값을 줘야함 > 이러한 과정이 번거러움