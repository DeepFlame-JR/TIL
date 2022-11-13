2022_DEKIT_Conference

Great PeoPle Data Engineering Confrence (https://school.programmers.co.kr/learn/courses/15230)

<br/>
<br/>

# Airflow 고도화하기
쏘카 3기 이호연

### Airflow in Socar
- Kubernetes 환경 운영 (GKE)
- 외부 데이터를 Bigquery에 적재를 주 목적으로 사용

#### 문제점
- 개발 환경이 불친절
- K8S 개발 환경 Airflow의 에러 발생 및 관리자/컴퓨팅 리소스 낭비
- 모니터링 환경/대응이 체계화되지 않음

### 전체 개발 환경 개선하기
- 프로그래밍 익숙치 않은 팀원들에게 Airflow 사용 러닝 커브를 낮추기
- 개발/디버깅하는 시간을 줄이기

#### 로컬 개발 환경 구축
- Docker Compose 기반의 로컬 노트북 환경
- GCP Service Account를 통한 인증 수단 활용
- Dag 파싱 효율화를 위한 .airflowignore 활용

#### 임팩트
- Airflow 서버 띄우는 시간 단축
- 개발 루프 시간 단축
- 유휴 airflow 노드를 점유했던 문제 해결
- docker 표준 환경을 통해 불안전성을 낮춤

### Airflow 스케일 업하기 
- SPOF 문제로 인해 Airflow 신뢰성이 중요
- Scheduler HA 설정을 통해 로드 밸런싱
- Meta DB 스케일업
- K8S 환경 개선
    - Node Pool 분리 및 Auto Scaling 적용
    - 사용자가 Task 리소스 직접 할당
- Clean up Dag 스케줄링 (밀리는 현상이 완화되어 성능 개선)

### 모니터링 고도화
- Dag이 늘어나며, 맥락을 파악하기 어려워짐
- 관리자는 airflow 인프라 환경(k8s)에 대한 모니터링을 할 수 있도록 해야함
    - Datadog로 대시보드 작성
    - Airflow metric 등 모니터링
- 정상적으로 스케줄링되지 않은 Dag을 모아서 10분에 한 번씩 알림 (Slack) 

### 보안
- Secret 저장소를 활용하도록 함
- 개인 사용자별 인증을 할 수 있도록 계정 제공
- GCP Secret Manager를 활용
- RBAC(Rule Based Access Control)을 제공
    - 팀별 액세스 패턴에 맞게 관리

<br/>
<br/>
<br/>

# 공공데이터 적재하기 
뱅크샐러드 2기 이은지

### 공공데이터 적재 프로세스
1. 데이터 정하기 
1. 데이터 사전조사
1. 데이터 파이프라인 설계 및 구현
1. 적재 확인 및 활용

API > PySpark Job > Airflow DAG 

#### 문제점
1. 많은 도메인 지식 필요
    - 다른 자료를 참고해야 함
1. 암묵적으로 사용하는 규칙대로 데이터를 제공
1. API 간에 의존성이 있음
    - 하나 값을 위해 많은 값을 참고해야함
    - 시장구분 코드 > API > 단축코드 > API > 종목명, 발행회사, 발행일 ... 

#### 후기
- 사전조사를 할 때는 많이 보기
    - 데이터 특성에 대한 이해는 넘치도록 공부해도 부족하다
- 외부요인에 대해 적극적으로 대응
    - 데엔이 핸들링할 수 없는 범위의 외부요인이라도 대응
    - 불안정한 API > 1시간에 한 번 retry 24
    - 공공데이터 클래스를 생성하고, 한 곳에서 관리하도록 구현

<br/>
<br/>
<br/>

# 스타트업에서 데이터 기반 의사결정 환경 구축기
노을 7기 김민종

#### 초기 문제점
- No DB, No Cloud
- 데이터 현황 파악, 분석 어려움
- 모델 분석적 성능 가시성 X

#### 도구
- 이 도구가 어떤 문제를 어떻게 해결할 수 있는가에 중점
- Airflow, Postgresql 등...

#### 단일진실 공급원
- 기존: 목적에 따라 분산되어 있는 엑셀 (무결성 보장 X)
- 개선: 소스시트 분석시트 분리
    - 소스 시트 일원화 후 데이터베이스 ETL

#### 데이터 분석 환경
- 기존: NAS에 분산 저장 > 컴퓨팅 자원 한계. 데이터 유실 가능
- 개선: Cloud 이전 (S3, Lambda + Object Batch)

#### ML 모델평가 추론 환경 개선
- 기존: NAS File + On Premise + 추론결과 file 저장
- 개선: S3 + Sagemaker Batch Transform + 추론결과 DB에 저장
    - 추론 코드를 반복적으로 돌리지 않아도 됨

#### 후기
- 문제를 외면하지 않고 해결
- 문제 정의/공유/스폰서확보
- 같은 문제를 겪는 동료와 해결방법 찾기 
- 문제를 나누어 하나씩 해결
- 좋아진 점을 공유하고 느낄 수 있도록 하기
- 변화를 만들며 느끼는 성취감 즐기기