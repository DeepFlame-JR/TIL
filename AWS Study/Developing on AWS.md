# Developing on AWS
- 개발 환경을 지원하도록 IAM 권한 구성
- AWS SDK를 사용한 클라우드 네이티브 애플리케이션 설계
- AWS 리소스를 사용하여 애플리케이션 모니터링 및 유지 관리

# AWS에서 개발 시작하기
- AWS 서비스에 프로그래밍 방식으로 액세스
- AWS: 인터넷 + 온디멘드 + 종량 요금제 + Infra as a code

### AWS REST API
- 요청: HTTP(S), SigV4, IAM 액세스 키
- 인스턴스 실행, 버킷 생성 등 대부분의 AWS 서비스를 호출할 수 있다
- 버전이 업데이트 중임. 특정 버전을 사용하기 위해서는 잠금 필요

#### HTTP 상태 코드
- 100시리즈: 정보
- 200시리즈: 성공
- 300시리즈: 리디렉션
- 400시리즈: 클라이언트 오류 (요청이 잘못됨)
- 500시리즈: 서버 오류

#### AWS 서비스 액세스
- HTTP(S) 요청/응답: AWS URL에 직접 접근하여 REST API에 접근한다
- SDK: 함수를 활용하여 REST API에 접근한다
    - AWS Management Console, CLI 모두 SDK를 활용한다
    - 친숙한 언어를 그대로 활용할 수 있음
    - HTTP 요청 서명을 쉽게할 수 있음

#### 사용할 SDK API 결정
- 하위 수준 API
    - 서비스 작업당 메서드가 1개 있음
    - 서비스: EC2, S3, DynamoDB 등
```python
def listClient():
    s3_client = boto3.client('s3')
    res = s3_client.list_object_v2(Bucket='mybucket')
```
- 상위 수준 API
    - 하위 수준 API보다 정리가 되어있음
    - 개념적 리소스당 1개의 클래스
    - 리소스: 버킷, 테이블 등...
```python
def listResource():
    s3_resource = boto3.resource('s3')
    bucket = s3_resource.Bucket('mybucket')
```

### AWS CLI
- 터미널 프로그램에서 AWS 서비스 호출
- aws + 서비스 + 하위명령 + 파라미터 형태
    - aws s3 ls s3:mybucket --recursive

#### 서비스 작업
- 동기식: 클라이언트가 요청을 하고 명령이 완료되기를 기다림
- 비동기식: 클라이언트가 요청을 하지만 명령이 완료되기를 기다리지 않음
    - 상태를 가져오기 위해 폴링: 요청을 지속적으로 확인
    - ACTIVE가 될 때까지 대기: 활성화 때까지 확인

#### IDE
- AWS 툴킷은 여러 IDE에서 사용 가능
- AWS Cloud9: 코드 작성, 실행, 디버깅을 위한 클라우드 IDE

# 권한
- 개발 환경을 지원하도록 권한 구성
- IAM 권한을 통한 인증

#### IAM
- AWS 서비스를 사용하기 위해 IAM 인증이 필요
    - 인증 (키 또는 id/pw)
- Root user: full access, 작업용 X, 빌링용
- IAM User: Account 내 다수 생성 가능
- IAM Group: IAM User의 권한을 한꺼번에 설정 가능
- IAM Role
    - 임시 키, 재사용 불가
    - Role 부여시 기존의 권한은 무시됨
    - 연동 유저 (SSO, Federation User)
    - 한꺼번에 설정 불가능
- Policy: IAM 권한 명시
    - 명시적 거부
    - 명시적 허용
    - 묵시적 거부

#### 보안 인증 우선순위
1. 코드 또는 CLI
1. 환경 변수
1. 보안 인증 정보 파일
1. 인스턴스 프로파일

#### 서명 버전4 (SigV4)
- 요청자의 ID 확인
- 전송 데이터 보호
- 재전송 공격에서 보호
- HTTP 권한 부여 헤더

# 스토리지
1. 블록 스토리지
    - EBS (하나의 EC2와 연결)
        - 범용 SSD
        - 프로비저닝된 IOPS SSD
        - 처리량 최적화 HDD
        - 콜드 HDD
    - 변경된 블록만 갱신 (DAS)
1. 파일 스토리지
    - EFS (여러 EC2와 연결)
    - 항상 전체 파일 갱신 (NAS)
1. 객체 스토리지
    - Amazon S3
    - 계층 구조가 없음. 플랫폼 구조.

## Amazon S3
- 리전 리소스 3개의 AZ에 복제 > 내구성(데이터가 사라지지 않음) & 가용성(필요할 때 바로 사용)
- 수평 확장 > 객체 수 제한 없음. 1개 객체 크기제한 5TB
- Standard / Standard IA / Onezone IA / Glacier(백업 및 아키이빙)
- 빅데이터 분석

#### 태그
- 객체 그룹화
- 비용 할당
- 자동화
- 액세스 제어 (IAM 정책, 버킷 정책 제어)
- 운영 지원/모니터링

#### S3 서비스 엔드포인트
- 가상 호스팅 방식 URL   
예) https://[bucketname].s3.[Region].amazonaws.com/[key]
- 웹 사이트 엔드포인트  
예) https://[bucketname].s3-website-[Region].amazonaws.com

#### AWS S3 CLI 명령
```cli
aws s3 ls
aws s3 mb s3://mybucket --region (mb: make bucket) 
aws s3 list-buckets --query 'Buckets[].Name'
```

## SDK를 활용한 S3 작업

### 버킷 작업
- 생성, 나열, 삭제, 구성 업데이트

#### 버킷 생성
- HeadBucket API: 버킷 존재 여부, 액세스 가능 여부 확인

```java
try{
    HeadBucketRequest request = HeadBucketRequest.builder()
        .bucket(myBucket)
        .build();
    HeadBucketRequest result = s3.headBucket(request)
    if(result.sdkHttpResponse().statusCode == 200){ } // Bucket existing
}
catch (AwsServiceException awsEx){
    switch(awsEx.statusCode()){
        case 404: // bucket 없음
        case 403: // 권한 에러
    }
}

// 버킷 생성까지 대기
WaiterResponse<HeadBucketRequest> waiterResponse = s3Waiter.waitUntilBucketExists(bucketRequestWait);
HeadBucketRequest.mathced().response().ifPresent(System.out::println);
```

### 객체 작업
- 업로드, 나열, 다운로드 등..
- 대량 작업: 복사, 동기화, 배치

#### 객체 업로드:PUT
- 객체 복사
    - 객체의 사본 생성
    - 원래 객체를 삭제하여 객체 이름 재지정
    - 여러 S3에 걸쳐 객체 이동
    - 객체 메타데이터 업데이트
    - 단일 객체 업로드(권장 100MB, 최대 5GB), 멀티 파트 업로드 (최대 5TB)

#### 데이터 검색: GET 및 HEAD
- 객체 및 메타데이터 
    - GetObject (바이트 범위를 가져올 수도 있음)
- 객체 메타데이터
    - HeadObject (메타 데이터 정보를 가져옴)
    - GetObjectAcl
    - GetObjectTagging

#### 객체 관련 작업: S3 Select
- 간단한 SQL 표현식으로 객체에서 데이터 하위 집합만 검색
- csv 파일 형식으로 받을 수 있음

#### 액세스 권한 부여
- 미리 서명된 URL을 통해 임시 권한 부여할 수 있음


# 데이터베이스
- 관계형: RDS, Redshift
- 키 값: Dynamo DB
- 그래프: Neptune
- 인메모리: ElastiCache

#### 관계형 DB vs 비관계형 DB
|관점|관계형|비관계형|
|---|---|---|
|데이터 스토리지|행 및 열|키 값, 문서, 와이드컬럼, 그래프|
|스키마|고정|동적|
|확장성|수직적|수평적|
|트랜잭션|지원|유동적|
|일관성|강력한 무결성과 일관성|최종 및 강력|

- 관계형 DB는 강력한 무결성과 일관성이 중요할 때 사용 
    - 신한 은행>우리 은행 이체, 신한에서는 돈이 빠졌는데, 우리 은행에 돈이 들어오지 않으면 안 됨  
    트랜잭션을 통한 데이터 무결성이 중요
- 비관계형 DB는 빅데이터 처리가 중요할 때 사용
    - 한 두개가 어긋나더라도 데이터 분석에 큰 문제가 없음

## Amazon DynamoDB
- 아마존의 키-값 형태의 NoSQL DB
- 서버리스 (완전관리형)

#### 기본 사항
- 파티션 키
    - 필수적으로 지정
    - 파티션 키 기반의 파티션에 데이터가 저장됨
    - I/O가 분산될 수 있도록 파티션 키를 지정하도록 권장
        - 일자로 지정했을 때, 특정 일자에서만 I/O가 증가됨
    - 높은 카디널리티 (중복도가 낮음)
    - 잘 알려진 키
- 정렬 키
    - 선택 사항
    - 기본 키 = 파티션 키 + 정렬 키

### 읽기 및 쓰기 처리량
- RCU: 최대 4KB 크기의 항목에 대한 초당 강력한 읽기 일관성 수
    - 1초에 4,000KB에 대한 강력 읽기 일관성: 1,000 RCU 필요
    - 최종 읽기 일관성: 두 DB를 읽고 최신의 데이터를 가져온다 > 최대 4KB 항목을 초당 2번 읽을 수 있음
- WCU: 초당 1KB 쓰기의 수
    - 1초에 1,000KB 쓰기: 1,000 WCU 필요
- 쿼리 성능에 비해 테이블 스캔의 성능은 모든 테이블을 읽어 성능이 좋지 않음

#### 보조 인덱스
기본이 아닌 키 속성에 근거하여 데이터 쿼리
- 로컬 보조 인덱스
    - 테이블의 파티션 키와 같은 파티션 키를 가짐
        - 테이블의 파티션 안에 존재
- 글로벌 보조 인덱스
    - 파티션 키가 다름

#### 적응형 용량
- 제한 최소화
- 필요한 만큼만 프로비저닝
    1. 4개의 파티션에서 100 WCU가 프로비저닝됨
    1. 3개의 파티션에서 50WCU가 요청되어서 150 WCU가 남음
    1. 150 WCU를 다른 파티션에서 사용할 수 있음

### Dynamo DB 액세스
```java
DynamoDbClient client = DynamoDbClient.builder()
    .region(Region.US_WEST_2)
    .build()
```

# 애플리케이션 로직 처리

## 컴퓨팅 서비스
- 인스턴스(EC2): 확장 가능한 컴퓨팅 용량
- 컨테이너(ECS, EKS): 완전 관리형 컨테이너 오케스트레이션
- 서버리스(Lambda): 이벤트 중심 서버리스 컴퓨팅
    1. 비동기 이벤트(push): 이벤트가 발생된 쪽에서 람다 함수를 호출
    1. 비동기 이벤트(pull): 이벤트를 쌓아두고, 람다가 이벤트를 한꺼번에 확인한다
    1. 동기 (직접 호출): API를 통해 직접 호출, 호출 권한 필요

#### Lambda 사용
- 이벤트 소스 > 함수 > 서비스
- 구조
    - 액세스 권한 + 트리거 
    - 런타임
    - 동시성(동시에 N개 처리), 메모리, 시간제한 (최대 15분)
- 단순한 구조로 CPU 성능을 향상하고 싶으면 메모리 설정을 조정하면 된다
- 레이어를 통해서 같은 환경을 재활용할 수 있다

#### 권한
- 이벤트 소스에서 Lambda를 호출할 권한 부여
    - AddPermission API 사용
- 실행 역할을 업데이트

#### Lambda 함수: 핸들러
- 이벤트 객체: 호출 시 전송되는 데이터
- 컨텍스트 객체: 현재 런타임 환경에 대한 정보
- 핸들러 함수: 호출 시 실행할 함수 (이벤트 객체, 컨텍스트 객체 포함)

#### Lambda 테스트
- 메모리 성능 테스트
- 시간 초과를 로드 테스트
- Lambda 한도 이해
    - 메모리 할당: 128 ~ 10,240MB
    - 최대 런타임: 15분
    - 버스트 동시성: 500 ~ 3,000
    - 동기식: 6MB / 비동기식: 256KB
    - /tmp 디렉터리 스토리지: 512MB

#### 오류 처리
- 호출 오류: 응답 코드 400 또는 500
    - 요청: 너무 크거나 유효하지 않음
    - 호출자: 권한이 없음
    - 계정: 최대 함수 인스턴스에 도달함, 요청이 너무 많음
- 함수 오류
    - 시간 초과
    - 구문 오류

# API 관리

## Amazon API Gateway
- 인증, 관리 컨트롤
- DDos 내성
- AWS 내 서비스들을 직접 접근하지 않도록 관리
- WebSocker API, HTTP API, REST API 활용


# 현대적 애플리케이션
변화하는 비지니스 환경에 빠르게 대응하는 SW  
- 아키텍처 패턴: 마이크로 서비스
- 소프트웨어 전송: DevOps
- 운영 모델: 서버리스

## 마이크로 서비스 (MSA, MicroService Architecture)
1. 작은 서비스
    - 큰 문제를 작게 분할하고, 해결한다
    - 결합도를 낮춘다
1. REST API
    - 요청: 기술 독립적
    - 응답: json, xml
1. 실행 환경이 독립적 (컨테이너 기술 활용)

#### 모놀리스 애플리케이션 디커플링
- 분해하기 간단한 서비스를 이용해 소규모로 시작
- 자주 변경되는 기능 디커플링

## DevOps
- 비지니스에 변화 빠르게 대응
- 마이크로 서비스가 빠른 배포에 유리

## 서버리스
- 서버를 고려하지 않고도 애플리케이션과 서비스를 구축할 수 있음
- 프로비저닝, 오토스케일링, 로드 밸런싱을 구성하지 않아도 됨
- 유휴 서버 비용을 거의 지불하지 않음
- 사용자는 핵심 제품에 집중할 수 있음
