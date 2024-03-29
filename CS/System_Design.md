System_Design

# 시스템 설계
- 최초 간단한 “시스템의 목표” 와 “시스템의 워크플로우” 가 주어지며, 이후 설계한 시스템에 맞게 상세와 제약사항을 추가
- 측정 사항
    - 모호한 문제를 건설적으로 해결할 수 있는지
    - 좋은 질문을 던질 능력이 있는지
    - 비용/보안 여러 측면에서 tradeoff 고려하는 능력이 있는지 확인
- 필요 역량
    - 기본적인 CS 지식의 깊이
    - 재료 (DB, Load Balancer, Stateless, Small Knowledge를 많이 알고 있어야함)
    <img src="https://user-images.githubusercontent.com/40620421/248440159-0b028992-775a-4e68-ba06-b80ac3e3b5de.png" width="400">


#### 4단계 접근법
1. 문제 이해 및 설계 범위 확정 (3~10분)
    - 구체적으로 어떤 기능을 만들 것인가? (Use Case)
    - 단계적이고, 체계적으로 문제를 정의
    - 사용자는 얼마나 커지는가? 회사에서 주로 사용하는 기술 스택은 무엇인가?
2. 개략적인 설계안을 제시, 면접관의 동의 (10~15분)
    - 청사진 제시 및 의견 구하기 (다이아그램)
        - 클라이언트, API, 웹서버, DB, 캐시, CDN, 메시지큐 등
    - 과정을 논리적으로 설명 + 상세 설계에서 집중해야 할 영역
3. 상세한 설계를 진행(10~25분)
    - 시간 관리 필요 > 컴포넌트 사이의 우선순위 정해야함
    - 시스템 성능에 대한 이야기
        - 신뢰성: 어떠한 장애가 있더라도 정상적으로 작동
        - 확장성: 빠르게 늘어나는 사용자 부하와 데이터를 처리
        - 유지보수성: 신기능을 추가하고, 기존 기능이 잘 작동하도록 해야함
4. 마무리(3~5분)
    - 설계를 다시 한 번 요약
    - 개선해야 할 점에 대한 얘기, 오류가 발생할 수 있는 포인트

## 재료
- 기본 컴퓨터 과학 지식: 자료 구조, 알고리즘, 운영 체제, 네트워크 등의 개념
- 시스템 아키텍처: 분산 시스템, 마이크로서비스 아키텍처, 클라우드 아키텍처 등 다양한 시스템 아키텍처에 대한 이해
- 확장성과 가용성: 수평 및 수직 확장, 로드 밸런싱, 캐싱, 데이터베이스 샤딩 등
- 데이터베이스 설계: 정규화, 비정규화, 인덱싱, 분산 데이터베이스 등의 개념
- 성능 최적화: 캐싱, 레플리케이션, 비동기 처리, 쿼리 튜닝 등의 기술
- 보안과 인증: 암호화, 인가 및 인증 프로토콜, 액세스 제어 등의 개념
- 대용량 데이터 처리: 배치 처리, 실시간 스트리밍, 대용량 데이터 저장소 등의 개념
- 시스템 디자인 패턴: 로드 밸런싱, 서킷 브레이커, 재시도 메커니즘 등의 패턴
- 클라우드 기술: 클라우드 서비스 제공 업체의 기능과 서비스 모델

## 예시

### URL 단축기 (URL Shortener) 시스템 설계
사용자가 긴 URL을 입력하면 짧은 고유한 URL을 반환하는 서비스를 설계하세요. 이 시스템은 많은 양의 트래픽을 처리하고, URL 간 충돌을 피하며, 확장 가능해야 합니다.

### 소셜 네트워크 피드 시스템 설계
사용자가 글을 작성하고, 친구들의 피드를 볼 수 있는 소셜 네트워크 피드 시스템을 설계하세요. 이 시스템은 사용자의 관심사와 친구 목록을 기반으로 피드를 개인화하고, 실시간으로 업데이트되어야 합니다.

### 온라인 쇼핑몰 아키텍처 설계
대규모 온라인 쇼핑몰 시스템을 설계하세요. 이 시스템은 상품 검색, 장바구니, 주문 처리, 결제 처리 등 다양한 기능을 제공하고, 사용자의 행동을 추적하여 개인화된 추천을 제공해야 합니다.

### 온라인 동영상 스트리밍 시스템 설계
대규모 온라인 동영상 스트리밍 플랫폼을 설계하세요. 이 시스템은 사용자에게 실시간으로 동영상을 제공하고, 다양한 장치 및 플랫폼에서 재생 가능해야 합니다.

### 분산 메시징 시스템 설계
대규모 분산 메시징 시스템을 설계하세요. 이 시스템은 실시간 메시지 전달, 메시지 큐, 이벤트 처리 등을 지원하고, 수평 확장이 가능해야 합니다.

## MLOps 시스템 디자인에서 고려해야할 사항
1. 모델 서빙
    - 실시간 요청에 대응이 가능해야 함
    - ML 모델을 실행하고 결과를 반환
    - 모델 버전 관리, 로깅, 모델 성능 모니터링 및 재학습 고려
    - 모델 롤백 및 A/B 테스트를 지원
1. 데이터 관리 시스템
    - 데이터 수집, 저장, 전처리 및 버전 관리 지원
    - 품질 검증, 데이터 분할 및 변환 고려
1. CI/CD를 활용
    - 모델의 자동화된 빌드, 테스트, 배포 프로세스를 구성
    - 모델의 학습, 배포 및 모니터링을 자동화하는 파이프라인
1. 모니터링
    - 모델의 예측 성능과 성능 변화를 모니터링하고 로그를 수집
    - 이상 탐지 및 경고 메커니즘을 구현
1. 재학습
    - 재학습하고 배포하는 프로세스를 자동화
    - 새로운 데이터 수집, 데이터 전처리, 학습 및 평가 과정을 고려