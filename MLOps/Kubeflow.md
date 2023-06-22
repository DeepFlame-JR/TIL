Kubeflow

# Kubeflow
- ML 워크플로우를 구축하고 실행하기 위한 플랫폼
    - 데이터 전처리, 모델 훈련, 모델 서빙 등의 작업을 자동화하고 확장성을 제공
- ML 구성요소들을 추상화하고, 재사용 가능하도록 함

<img src="https://www.kubeflow.org/docs/images/kubeflow-overview-platform-diagram.svg" width="600">

## 주요 구성
- 컴포넌트
    - 데이터 전처리를 위한 컴포넌트, 모델 훈련을 위한 컴포넌트, 모델 서빙을 위한 컴포넌트 등을 제공
    - 이를 조합하여 사용자 정의 워크플로우를 구성
- 메타데이터 및 자원 관리
    - Kubeflow는 워크플로우 실행과 관련된 메타데이터를 추적하고 저장
    - 실행 기록, 실험 결과, 모델 버전 등을 관리
    - Kubernetes 클러스터에서 자원 관리를 수행하여 기계 학습 작업의 자원 할당과 확장을 지원
    - 모델 버전 관리
- Jupyter Notebook과 워크플로우 에디터
    - Kubeflow는 Jupyter Notebook을 통해 대화형 개발과 실험을 지원
    - 워크플로우 에디터를 통해 워크플로우를 시각적으로 디자인하고 관리
- 모델 서빙
    - 실시간 예측 서비스나 배치 예측 작업 등에서 모델을 활용
    - TensorFlow Serving과 Seldon Core와 같은 모델 서빙 프레임워크와 통합

### 구성 요소
1. Central Dashboard
    - 파이프라인, 실험 내역, 결과 등을 확인
1. JupyterHub
    - ML 프로젝트의 펏 시작인 프로토타이핑과 실험을 할 수 있는 Jupyter Notebook 제공
1. Pipelines Argo
    - 컨테이너 기반의 job orchestration
    - 도커 이미지를 기반으로 job을 실행
1. Pipeline
    - task들을 나열한 워크플로우
        - task는 컨테이너 실행이나 결과 파라미터 등을 의미함
        - DAG 형식으로 task를 구성
    - KFP 구성 과정
        1. Python KFP SDK의 DSL(domain-specific language)를 통해서 파이프라인을 생성
        1. KFP SDK's DSL compiler를 통해서 YAML로 컴파일
        1. KFP backend로 파이프라인을 실행 > K8S의 pod가 생성되고 파이프라인이 실행됨
        1. KFP Dashboard를 통해서 진행상황 모니터링
    - Docker container image > Pipeline component > Pipeline
1. Katlib
    - 모델 튜닝(AutoML)을 통한 하이퍼파라미터 최적화
1. TFJobs
    - 비동기 학습이나 오프라인 추론
1. KFServing
    - 온라인 인퍼런스 서버를 KFServing으로 배포할 수 있음
1. MinIO
    - 파이프라인 간의 저장소 기능을 함
    - 파이프라인 중간에 생기는 부산물을 저장


## 워크플로우
<img src="https://www.kubeflow.org/docs/images/kubeflow-overview-workflow-diagram-1.svg" width="600">

- 실험 단계 
    1. 문제 상황 확인, 데이터 분석
    1. ML 알고리즘 선택, 코드 작성
    1. 모델을 훈련하며 실험
    1. 모델의 하이퍼 파라미터 수정
- 상품 단계
    1. 데이터 전처리
    1. 모델 훈련
    1. 모델을 온라인으로 서빙, 예측값 생성
    1. 모델 성능 모니터링

    
### 컴포넌트
- 재사용 가능한 ML 학습의 단위
    - 데이터 전처리, 특성 추출, 모델 훈련, 평가 등
    - 독립적으로 실행, input/output을 통해 결과를 주고받을 수 있음
    - 각 컴포넌트가 실행될 때는 Artifacts가 생산됨 (Model, data, result, log 등...)
- 컴포넌트 = Component Contents + Component Wrapper 
    - Component Content: Python code (import + python code + generates artifacts)
    ```python
    # --environment
    import dill
    import pandas as pd

    from sklearn.svm import SVC

    # --python code
    train_data = pd.read_csv(train_data_path)
    train_target= pd.read_csv(train_target_path)

    clf= SVC(kernel=kernel)
    clf.fit(train_data)

    # --generates artifact
    with open(model_path, mode="wb") as file_writer:
        dill.dump(clf, file_writer)
    ```
    
    - Component Wrapper: Component Content + Configuration
    ```python
    def train_svc_from_csv(...):
        # Component Contents
    ```

    - Kubeflow 포맷으로 변환
    ```python 
    from kfp.components import create_component_from_func

    @create_component_from_func
    def train_svc_from_csv(...):
        # Component Contents
    ```

#### InputPath/OutputPath
- 컴포넌트 간에 Input/Output은 json으로만 전달됨 > Model과 같은 복잡한 데이터는 path를 통해서 전달
    ```python
    from kfp.components import InputPath, OutputPath

    def train_from_csv(
        train_data_path: InputPath("csv"),
        train_target_path: InputPath("csv"),
        model_path: OutputPath("dill"),
        kernel: str,
    ):
    ```

#### Environment
- 컴포넌트에 이미지와 package 등의 환경을 설정할 수 있음
```py
    @partial(
    create_component_from_func,
    packages_to_install=["dill==0.3.4", "pandas==1.3.4", "scikit-learn==1.0.1"],
)
def train_from_csv(
    train_data_path: InputPath("csv"),
    train_target_path: InputPath("csv"),
    model_path: OutputPath("dill"),
    kernel: str,
):
```

### Pipeline
- 컴포넌트 집합과 순서를 정의한 개념 (DAG)
- 컴포넌트 간의 상호작용을 정의하여 그래프 구조를 표현
    - 컴포넌트: 머신러닝 워크플로우의 한 단계
    - 그래프: 파이프라인의 시각적 표현
    - 실험: 파이프라인을 다양한 설정으로 돌려볼 수 있음
- 조건 분기를 사용할 수 있음
- Pipeline Config: 컴포넌트를 실행하기 위한 Config들을 모아둠
- 예시
    ```python
    @create_component_from_func
    def print_and_return_number(number: int) -> int:
        print(number)
        return number

    @create_component_from_func
    def sum_and_print_numbers(number_1: int, number_2: int):
        print(number_1 + number_2)

    @pipeline(name="example_pipeline")
    def example_pipeline(number_1: int, number_2: int):
        number_1_result = print_and_return_number(number_1)
        number_2_result = print_and_return_number(number_2)
        sum_result = sum_and_print_numbers(
            number_1=number_1_result.output, number_2=number_2_result.output
        )
    ```

#### 환경 설정
- Pipeline Name, Resource 등을 설정할 수 있음
```py
@pipeline(name="example_pipeline")
def example_pipeline(number_1: int, number_2: int):
    number_1_result = print_and_return_number(number_1).set_display_name("This is number 1")
    number_2_result = print_and_return_number(number_2).set_display_name("This is number 2")
    sum_result = sum_and_print_numbers(
        number_1=number_1_result.output, number_2=number_2_result.output
    ).set_display_name("This is sum of number 1 and number 2").set_gpu_limit(1).set_memory_limit("1G")
```

### Run
- 실행된 Pipeline을 나타냄
- Run은 고유의 ID를 가지고, 모든 Artifacts를 저장
    - 모든 단계의 Output을 추적할 수 있음




## Pipeline 예시
설치 참고
- https://deep-flame.tistory.com/43
- `kubectl port-forward svc/istio-ingressgateway -n istio-system 8080:80`

#### Hello World
```python
import kfp

KUBEFLOW_HOST = "http://127.0.0.1:8080/pipeline"

# 함수 정의
def hello_world_component():
    ret = "Hello World!"
    print(ret)
    return ret


# 함수를 컴포넌트로 변경
@kfp.dsl.pipeline(name="hello_pipeline", description="Hello World Pipeline!")
def hello_world_pipeline():
    hello_world_op = kfp.components.func_to_container_op(hello_world_component)
    _ = hello_world_op()


if __name__ == "__main__":
    # 컴파일한 후 등록하는 방법
    kfp.compiler.Compiler().compile(hello_world_pipeline, "hello-world-pipeline.zip")
    # 바로 등록
    kfp.Client(host=KUBEFLOW_HOST).create_run_from_pipeline_func(
        hello_world_pipeline, arguments={}, experiment_name="hello-world-experiment"
    )
```

#### Add number pipeline
```python
import kfp
from kfp import components
from kfp import dsl

EXPERIMENT_NAME = 'Add number pipeline'
BASE_IMAGE = "python:3.7"
KUBEFLOW_HOST = "http://127.0.0.1:8080/pipeline"

# 컴포넌트 정보 세팅
@dsl.python_component(
    name='add_op',
    description='adds two numbers',
    base_image=BASE_IMAGE 
)
def add(a: float, b: float) -> float:
    print(a, '+', b, '=', a + b)
    return a + b

# 함수를 pipeline operation으로 변경
add_op = components.func_to_container_op(
    add,
    base_image=BASE_IMAGE,
)

# 파이프라인 정보 세팅
@dsl.pipeline(
    name='Calculation pipeline',
    description='A toy pipeline that performs arithmetic calculations.'
)
def calc_pipeline(
        a: float = 0,
        b: float = 7
):
    add_task = add_op(a, 4) 
    add_2_task = add_op(a, b)
    add_3_task = add_op(add_task.output, add_2_task.output)


if __name__ == "__main__":
    arguments = {'a': '7', 'b': '8'}
    
    kfp.Client(host=KUBEFLOW_HOST).create_run_from_pipeline_func(
        calc_pipeline,
        arguments=arguments,
        experiment_name=EXPERIMENT_NAME)
```

#### Parallel
```python
import kfp
from kfp import dsl
EXPERIMENT_NAME = 'Parallel execution' 
KUBEFLOW_HOST = "http://127.0.0.1:8080/pipeline"

def gcs_download_op(url):
    return dsl.ContainerOp(
        name='GCS - Download',
        image='google/cloud-sdk:272.0.0',
        command=['sh', '-c'],
        arguments=['gsutil cat $0 | tee $1', url, '/tmp/results.txt'],
        file_outputs={
            'data': '/tmp/results.txt',
        }
    )


def echo2_op(text1, text2):
    return dsl.ContainerOp(
        name='echo',
        image='library/bash:4.4.23',
        command=['sh', '-c'],
        arguments=['echo "Text 1: $0"; echo "Text 2: $1"', text1, text2]
    )


@dsl.pipeline(
    name='Parallel pipeline',
    description='Download two messages in parallel and prints the concatenated result.'
)
def download_and_join(
        url1='gs://ml-pipeline-playground/shakespeare1.txt',
        url2='gs://ml-pipeline-playground/shakespeare2.txt'
):
    download1_task = gcs_download_op(url1)
    download2_task = gcs_download_op(url2)

    # 두 개의 task가 모두 끝날 때까지 기다린다
    echo_task = echo2_op(download1_task.output, download2_task.output)


if __name__ == '__main__':
    # kfp.compiler.Compiler().compile(download_and_join, __file__ + '.zip')
    kfp.Client(host=KUBEFLOW_HOST).create_run_from_pipeline_func(
        download_and_join,
        arguments={},
        experiment_name=EXPERIMENT_NAME)
```
<img src="https://user-images.githubusercontent.com/40620421/232314243-b9580d56-7281-45a2-90dd-27648ffe2b94.png" width=600>


#### Control 
- 결과에 따라서 어떻게 움직이는지 제어
```python
from typing import NamedTuple
import kfp
from kfp import dsl
from kfp.components import func_to_container_op, InputPath, OutputPath

@func_to_container_op
def get_random_int_op(minimum: int, maximum: int) -> int:
    import random
    result = random.randint(minimum, maximum)
    print(result)
    return result


@func_to_container_op
def flip_coin_op() -> str:
    import random
    result = random.choice(['heads', 'tails'])
    print(result)
    return result


@func_to_container_op
def print_op(message: str):
    print(message)


@dsl.pipeline(
    name='Conditional execution pipeline',
    description='Shows how to use dsl.Condition().'
)
def flipcoin_pipeline():
    flip = flip_coin_op()
    with dsl.Condition(flip.output == 'heads'):
        random_num_head = get_random_int_op(0, 9)
        with dsl.Condition(random_num_head.output > 5):
            print_op('heads and %s > 5!' % random_num_head.output)
        with dsl.Condition(random_num_head.output <= 5):
            print_op('heads and %s <= 5!' % random_num_head.output)

    with dsl.Condition(flip.output == 'tails'):
        random_num_tail = get_random_int_op(10, 19)
        with dsl.Condition(random_num_tail.output > 15):
            print_op('tails and %s > 15!' % random_num_tail.output)
        with dsl.Condition(random_num_tail.output <= 15):
            print_op('tails and %s <= 15!' % random_num_tail.output)


@func_to_container_op
def fail_op(message):
    """Fails."""
    import sys
    print(message)
    sys.exit(1)


@dsl.pipeline(
    name='Conditional execution pipeline with exit handler',
    description='Shows how to use dsl.Condition() and dsl.ExitHandler().'
)
def flipcoin_exit_pipeline():
    exit_task = print_op('Exit handler has worked!')
    with dsl.ExitHandler(exit_task):
        flip = flip_coin_op()
        with dsl.Condition(flip.output == 'heads'):
            random_num_head = get_random_int_op(0, 9)
            with dsl.Condition(random_num_head.output > 5):
                print_op('heads and %s > 5!' % random_num_head.output)
            with dsl.Condition(random_num_head.output <= 5):
                print_op('heads and %s <= 5!' % random_num_head.output)

        with dsl.Condition(flip.output == 'tails'):
            random_num_tail = get_random_int_op(10, 19)
            with dsl.Condition(random_num_tail.output > 15):
                print_op('tails and %s > 15!' % random_num_tail.output)
            with dsl.Condition(random_num_tail.output <= 15):
                print_op('tails and %s <= 15!' % random_num_tail.output)

        with dsl.Condition(flip.output == 'tails'):
            fail_op(message="Failing the run to demonstrate that exit handler still gets executed.")


if __name__ == '__main__':
    # Compiling the pipeline
    kfp.compiler.Compiler().compile(flipcoin_exit_pipeline, __file__ + '.yaml')
```
<img src="https://user-images.githubusercontent.com/40620421/232314491-25457f28-6a8e-4a1c-bf13-652448f5adeb.png" width=600>
