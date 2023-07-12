2023_MLOps_Community_Meetup

# 2023 MLOps Community Meetup

## 제조업 MLOps 도입 사례
- LG 화학, 전성범
- Factory 데이터 활용

### MLOps 필요성
- 석유화학 적용 후 AI 결과 데이터를 활용한 사전 공정 대처 실시 (86% > 94%)
- 확산 필요
    - 표준화된 Data 및 AI 학습/배포 파이프라인 필요

### Use Case
- 주요 목적
    1. AI 개발 로직을 운영에서 수정없이 그대로 활용 가능한 체계
    1. 모델 성능 유지를 위한 자동 재학습 및 모니터링
- 과정
    1. 전사적 DX Infra 구축
        - Datalake 구축: 원천 시스템 백업을 통해 Raw Data 확보
        - Feature Store 구축: 표준화 Datapipe를 통한 분석용/학습용 데이터 체계 구축
        - AIB 플랫폼: AI 및 빅데이터 분석을 위한 Public Cloud 기반의 분석 플랫폼
        - AI 활용 시스템: 실시간 예측 파이프라인 및 시각화
    1. 효율적인 데이터 수집/전처리 > 데이터 처리 표준화
        - 분석가들이 활용할 수 있는 형태의 데이터로 변환
            - DB > Data Lake(S3) > Feature Store(Snowflake) > AI 학습
            - S3가 느리기 때문에 Snowflake에 적재를 함
        - 표준화된 Data pipe라인을 통해서 AI 학습과 추론에서 동일한 데이터 구조로 표준화
    1. 다수 AI 학습에 따른 운영 시간/비용 > 훈련 자동화
        - Feature Store > (AI 학습 > AI 배포) batch
        - 119개 중 50개만 적용함
    1. 운영 모니터링 체계
        - AI 성능을 유지/관리하기 위해 전사 관점의 DX 운영 체계 구축
        - DQ Validation Framework
            - 총 6개의 품질 지표를 활용하여 자동 검증
            - 주기적인 모니터링 실시
        - AI Drift 모니터링
            - Data/Performance 지표를 통해 재학습/오류 확인
            - 운영 Process에 따라 단계별 대응
        - DX 서비스 활용
            - 모델 활용도 지표 모니터링


## Naver Glace MLOps 도입 사례

### 프로젝트
- OCR api를 연동
- 자체 모델 개발 (음식 분류 & 영수증 확인)
- 현재
    1. 음식 이미지 메뉴 분류
    1. 리뷰 이미지 종류 분류
    1. 리뷰 이미지 품질 검증
    1. 영수증 이미지 분류
    1. 이미지 분위기 분류
    1. POI Matching (영수증 > 업체 매칭)
    1. Menu Mathcing (영수증 > 메뉴 매칭)

###  시스템 구조
- Building vs Buying
    - Building
        - 사내 플랫폼 자체 구성
        - AI 기술이 부서 핵심 과제
        - AI 생태계가 빠르게 변하기 떄문에 이를 따르기 위함
- CI/CD 파이프라인 자동화
    - ML 학습 환경 > ML 서빙 환경 > 비동기(천천히, 자원 최적화를 위해서)/HTTP(빠르게)


## MLOps로 반도체 장비 리얼타임 이상 탐지하기
- 제조업 ML 특징
    - 제조업 도메인에서 문제/데이터마다 특화된 ML 모델 필요
    - 거대 모델을 범용적으로 활용할 수 있음
- 반도체 장비 이상탐지 연구
    - 과거 데이터 기반 모델 학습
    - 정상 데이터 대비 이상 데이터 비율
- 문제점
    - 훈련 후 실시간 수집 데이터에 대해 평가 > 훈련 성능 재현 X
    - Why? 장비 Recipe 변경, 부품 교체 등...
    - 성능 저하 문제를 해결하기 위해서 빠르게 분석/재학습/배포를 해야함

### 문제 정의
- Drill 장비 10대의 정상/이상 판별
    - Down Time 예방
    - 데이터/이상 탐지 모델 기반 장비 특성 이해
    - 고객사의 자체적 이상탐지 서비스 운영

### 문제 해결
- 모델
    - AutoEncoder
        - x > 모델 > x^ 복원함
        - 복원을 제대로 하지 못 하면 이상한 데이터라고 판단
- 인프라
    - Kubeflow 활용 (공장에 On-Premise로 구성)
    - Link 활용 (Jupyter Notebook 기반 Pipeline Build)
    - MLflow 활용 (훈련 데이터 저장)
        - Kubeflow pipeline run - MLflow run
    - Seldom Core 활용 (모델 서빙)

### 배포시, 문제
1. 센서가 하나라도 고장났을 때, error가 나타남
    - 모델을 2개로 생성 (Core한 데이터로만 추론 / 모든 데이터로 추론)
1. 지속적 학습 필요
    - 데이터 검증
        - 발전기 이벤트 포함 비율
        - 데이터 범위 기반 이상 판단 알고리즘
        - 데이터 클러스터링 알고리즘




