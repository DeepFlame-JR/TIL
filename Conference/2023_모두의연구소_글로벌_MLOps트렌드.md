2023_모두의연구소_글로벌_MLOps트렌드

https://www.youtube.com/watch?v=DRIEKB9smBY&ab_channel=%EB%AA%A8%EB%91%90%EC%9D%98%EC%97%B0%EA%B5%AC%EC%86%8C
Makinarocks 김민규님.전종섭님.

# MLOps
- AI 개발과 운영을 위한 도구 또는 일련의 활동
    - 사람의 의존을 낮춤
    - 생산성을 향상

#### AI 도입 이유
1. 반복되는 일을 AI로 해결하기 위해서 
1. 종업원의 숙련도를 올리기 위해서
1. HR 리크루팅을 위해서 
1. 사람이 할 수 없는 일을 AI로 대체 

#### MLOps를 도입해야하는 이유
- ML 모델이 Production이 될 때까지 아래와 같은 과정이 필요
    1. 문제 정의
    1. 데이터 수집 및 처리
    1. ML 모델 개발/학습
    1. ML 모델 배포
    1. ML 모델 운영
- 각 단계에서 제대로 수행되지 않는다면 이전 단계로 돌아갈 가능성이 있음
- 각 단계를 잘 처리할 수 있는 솔루션 도입 필요

#### 도입이 어려운 이유
- 태동하는 시장 
    - ML이 있어야 적용이 되는 시장
    - ML 모델을 도입하면 MLOps를 도입할 수 밖에 없음
- 각자 원하는 것이 다르고, 구성을 다르게 하기 때문에 복잡도가 높다. 
    - A는 어느정도만 자동화
    - B는 모든 과정을 자동화

#### 트렌드
1. 산업 특화: 일반적인 MLOps 툴이 아닌 산업에 특화된 툴
1. 시각화: 고객이 원하는 형태를 지속적으로 모니터링
1. 고객에 맞는 Customized 환경 제공: 쉬운 파이프라인 버전 관리, Auto ML 등

#### 한국 시장
- 문제점
    - 고객마다 MLOps 요구 사항이 천차만별
    - 높은 규제로 도입에 고심
    - 회사마다 MLOps에 대한 이해도가 다름
    - MLOps 도입시 효과에 대한 경영진의 의문점
- 방법론
    - Customization이 가능한 확장성
    - 폐쇄망, Private cloud 등 다양한 고객사 인프라 환경 지원 가능
    - 직관적이 UI/UX
    - 라이선스 충돌 회피
- 고려사항
    - 주 이용자 (누구를 위한?)
        - DS vs Non-DS
        - 자유도, 기능 범위 등
    - 지속 협업 가능성 (우리가 필요한 기능은?)
        - feature 및 요건은 변화할 수 밖에 없음 > 이를 적절히 지원받을 수 있을까?
    - 문제 해결의 Recipe
        - 도구는 도구일 뿐, 도구가 확보되었다고 문제가 해결되지 않음

## 연구실 밖으로 나온 모델
- 서비스? 머신러닝 결과를 통해서 가치를 얻는 것
- 연구는 자유로운 세계 <-> 좋은 서비스는 표준화에서 나옴
    - 적절한 수준의 자유도와 표준화가 필요함
        - 원래 개발에서는 DevOps를 통해서 이러한 문제를 해결
        - ML에서는 MLOps라고 부름
    - 파이프라인으로 소통!
        - 모델로 소통하지 않는다
        - 모델을 생성할 수 있는 파이프라인으로 소통 (Data Scientist이 모델을 생성하는 과정을 받음)
        - Kubeflow, Airflow
            - Kubeflow (Pythonic한 방법, 다양한 기능들을 지원)

#### Open Source를 통한 플랫폼 구축
- Kubeflow + MLflow
    - run을 어떻게 관리?
        - Train model > Validate model > Save model (각각 1개의 run에 저장 > 3개의 run)
        - redis ({kubeflow run id}:{mlflow run id})
        - mlflow run으로 저장
- 모델 서빙
    - `셀덤 CORE`, BENTOML
    - 제조업: 장비마다 발생하는 데이터를 통해서 모델을 생성해야함
        - 상용적인 ML 모델이 필요하지 않음
    - Kubeflow > MLflow > Best Model > CORE
- ML metadata & Reproduce
    - 의도하지 않는 결과가 나타나는 경우가 있음
        - 모델의 구조적인 문제인지, 데이터 문제인지 확인 필요
    - Pipeline run, MLflow run의 메타 데이터 (하이퍼 파라미터 등)을 저장

#### 문제점
1. KFP 파이프라인 작성이 너무 어려움
    - 일반적인 Python 사용법 이상의 지식 요구
    - 파이프라인 작성 후 실행시 infra 관련 버그가 자주 일어남
1. kubeflow 환경의 reproduce가 너무 어려움

#### 해결
1. 파이프라인 작성이 어렵다?
    - 기능적 > 파이프라인 작성을 쉽게 만들어 주자
    - 절차적 > 모델 개발과 파이프라인 개발을 분리하자
1. reproduce가 어렵다
    - 기능적 > 파이프라인 작성 환경과 모델 개발 환경을 맞추자

## 마키나락스 제품
1. Runway > Dataset 가져오기
1. Link > Jupyter > Dataset 가져오기 등을 파이프라인으로 변경해줌 > 파이프 라인 저장 > Runway에서 확인 가능
1. 학습 시에 Runway에 모델 적재 > Deploy 가능 > POST api를 통해서 예측 값을 가져올 수 있음
1. Pipeline에서 Parameter만 바꿔서 새 모델을 빠르게 생성 가능
1. 이상 탐지: 데이터셋에 들어가서 모델을 import하고 예측값 reproducing 가능