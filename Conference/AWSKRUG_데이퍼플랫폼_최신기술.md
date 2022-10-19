AWSKRUG_데이퍼플랫폼_최신기술
- AWS 한국 사용자 모임 (https://awskrug.github.io/)  
- 2022.09.21 강웅석

# 클라우드 데이터 플랫폼을 구성하는 최신 기술 알아보기

## 데이터 플랫폼
- 아주 다양한 형태의 데이터를 (정형, 비정형, 반정형) 엄청나게 많이 저장할 수 있고 (PB+)    `Distributed Storage`  
    빠른 데이터 조회가 가능하고 모든 사내 구성원이 데이터를 조회할 수 있어야 하고    `Distributed Query Engine`  
    데이터를 시각화할 수 있어야 하고    `Visualization`   
    내가 원하는 데이터를 빠르게 잘 찾을 수 있어야 하고  `Discovery`
    개발자가 아닌 사람도 편하게 볼 수 있어야 하고   `Less-Code`         
    저렴해야함   

### 과거와 현재
- 과거 (소수)
    - Hadoop, CloudEra, Amazon Redshift/Athena, BigQuery
- 현재 (다수)
    - AWS Athena, Glue, EMR 등
    - Google BigQuery 등
    - MS Azure Databricks 등
    - Databricks, snowflake, MySQL(분산 DB 도입 중)
    - dremio(DL), FIREBOLT(DW) (현재 급부상 중)

### Why 데이터 플랫폼?
- 데이터를 통해 서비스, 비지니스의 과거/현재/미래를 알 수 있다
    - 다양한 지표와 통계
    - 데이터를 통한 의사결정
    - 데이터 기반 서비스

## 다양한 클라우드 데이터 플랫폼 패러다임

### Data Warehouse
- DB w/ `Distributed Storage`, `Query Engine`
- Redshift, Athena, BigQuery, Snowflake, FIREBOLT
- 장점
    - 사용자가 신경쓸 것이 많이 없음
    - 내장 metadata이기에 `Schema evolution` 같은 것이 자유로움 (돈이 들 수 있음)
        - Schema evolution: 데이터가 쌓이고 난 후 변경되었을 때 어떻게 데이터를 저장하는지 매커니즘 용어
    - 단순 query engine 이상 기능을 제공하는 경우가 있음
    - SQL native로 관리 가능
- 단점
    - ingestion 과정이 있음
    - DW에서 지원하는 기능만 사용가능 (image, audio 정제는 어려울 수 있음)
    - 가격이 부담
    - component 별로 데이터가 파편화된다 (real-time BI serving을 위해서는 BigQuery만으로는 부족)
    - 한 번 들어간 데이터는 빼기 어렵다 (빼려면 돈이 많이 듬)

#### AWS Redshift
- 대용량 병렬 처리 (MPP) 시스템
    - 클러스터를 리더노드와 컴퓨팅 노드로 구성됨
- 열 기반 데이터 스토리지
- `OLAP`에 가까움
    - OLTP: 트랜잭션 기반으로 하는 데이터 작업
    - OLAP: 데이터들을 효과적으로 활용하기 위해 여러 관점에서 분석해보는 정보화 작업

#### AWS Athena
- Presto 기반의 대화식 쿼리 서비스
- 서버리스로 `S3의 데이터`에 대한 쿼리를 간단하게 사용 가능
- 표준 SQL 활용 가능
- 쿼리당 비용 지불

#### Google BigQuery
- Dremel 기반의 대화식 쿼리 서비스
- 서버리스로 `속도가 빠른 SQL Engine` 지원
- 표준 SQL 활용 가능 
- 스토리지와 컴퓨팅이 분리되어 탄력적인 확장성을 가짐
- BigQuery에 저장된 데이터를 로드하지 않고 쿼리 가능
- 데이터 실시간 분석 실행 가능

#### Snowflake
- 클라우드 네이티브 아키텍처, Data LakeHouse 서비스 제공
- 스토리지와 컴퓨팅이 분리되어 탄력적인 확장성을 가짐
- 직관적인 SQL 인터페이스를 통한 사용자 친화적 서비스 제공
- AWS, GCP, Azure 모두 제공

### Data Lake
- Distributed Storage / Distributed Query Engine / Metastore
- S3, Delta Lake, dremio
- 장점
    - 아무 데이터나 막 저장하면서 쓸 수 있다 (ELT)  
        `일단 S3에 데이터를 넣어라!`
    - 별도의 ingestion 과정이 필요 없음
    - Storage가 SSoT가 되기에, 풍부한 storage 기능 사용 가능 + 입맛에 맞는 query engine 사용 가능
    - 대부분의 클라우드는 storage가 제일 저렴! 또한 tiering도 가능
    - External, schema-on-read 방식(데이터가 정리되지 않아도 스키마를 읽을 때 주입)의 비교적 유연하고 programmatic API를 지원하는 metadata System: Hive, Glue, Dataproc metasotre 등
- 단점
    - Underlying storage 제약을 그대로 적용 (rename, ACID, streaming 등)
    - 성능이 천차만별 (데이터 타입이 정제되지 않아 집계가 오래 걸릴 수 있음)
    - Lake를 구성하는 component(환경 설정)가 너무 많음 (storage, query engine, metastore)
    - 데이터 관리가 상대적으로 어려움
    - External schema 관리가 복잡함 (file format 별로, store 별로)
    - SQL 만으로 데이터 관리를 할 수 없음

### Data Lakehouse?
- Data Warehouse & Data Lake
- Snowflake, databricks

#### Databricks
- `Delta Lake`와 함께 사용하여 Data LakeHouse 서비스 제공
    - 데이터 레이크 바로 위에 데이터 웨어하우스 계층이 가진 높은 안정성, 성능을 제공
    - 데이터 웨어하우스 계층을 제거함으로써 복잡성, 비용, 운영 오버헤드를 줄임
- Data Warehouse보다는 Data Lake에 포지셔닝 (스트리밍, 기계 학습 등에 중점)
- AWS, GCP, Azure 모두 제공

## 차세대 데이터 플랫폼 기술 살펴보기
- DW, DL의 단점을 극복할 수 없을까? 
- `Table Format`: 데이터 레이크와 상호작용하여 테이블로 추상화 (Hive가 1세대)
- 메타 데이터를 활용하여 무거운 작업을 처리함
- DELTA LAKE, Apache ICEBERG, Hide

### 기능
- 데이터 조작어(DML)을 통한 풀 스키마 full schema Evolution
- `ACID 트랜잭션`, 최적화된 스티리밍, 로깅, 캐싱, 데이터 레이아웃 최적화
- Time Travel & Rollback (`Snapshot`을 통한 재생성 가능한 쿼리 제공)
    - Time Travel: 모든 시점의 과거 데이터에 액세스 가능
- 오픈 소스

### 프로덕션 사용?
- Delta Lake: Data bricks와 연결
- ICEBERG: Spark, Snowflake 등와 연결

## QnA
- Athena vs BigQuery
    - BigQuery가 조금 성능이 좋음
    - 가격은 Athena가 쌈
    - 엔지니어가 어디에 더 익숙한 가?