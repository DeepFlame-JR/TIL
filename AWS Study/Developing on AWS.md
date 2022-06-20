# Developing on AWS
- 개발 환경을 지원하도록 IAM 권한 구성
- AWS SDK를 사용한 클라우드 네이티브 애플리케이션 설계
- AWS 리소스를 사용하여 애플리케이션 모니터링 및 유지 관리

# AWS에서 개발 시작하기
- AWS 서비스에 프로그래밍 방식으로 액세스

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
    - 인증 (키, id/pw)
- Root user: full access, 작업용 X, 빌링용
- IAM user
- IAM Group
- IAM Role: 임시 키, 재사용 불가
- Policy 

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

#### 데이터 검색: GET 및 HEAD
- 객체 및 메타데이터 
    - GetObject
- 객체 메타데이터
    - HeadObject
    - GetObjectAcl
    - GetObjectTagging


# 현대적 애플리케이션
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
1. 실행 환경이 독립적 (컨테이너)

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