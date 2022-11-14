2022_Upstage_추천AI
- 2022.11.10
- https://www.youtube.com/watch?v=tibwAh1t2UI
- https://www.upstage.ai/events/recsys2022?utm_source=linkedin&utm_medium=click&utm_campaign=Rec2022

<br/>
<br/>

# 좋은 추천, 잘 맞는 추천, 새로운 추천
부제: RecSys2022 논문과 함께 보는 추천 시스템
- 왜 추천 시스템에 관심이 많은가?
    - 매출증대가 가장 큼 
    - AND 빠르고, 정확한 추천 > 고객 가치, 브랜드, 신뢰에 연관됨
- 추천은 생각보다 많은 요소들이 영향을 줌

## Countering Popularity Bais by Regularzing Score Differences
이원도 (서울대 융합과학기술대학원 지능정보융합학과)

### 연구 동기 - 인기 편향 문제
- 추천 시스템은 아이템에 대한 유저 선호 점수를 계산하고, 가장 높은 아이템을 유저에게 추천
    - pos 아이템, neg 아이템을 분류 > pos 아이템은 선호점수가 높게, neg 아이템은 낮게 모델이 학습
- 추천 시스템 예측은 `인기 편향`의 문제를 가짐
    - 소수의 인기있는 아이템에 대한 소비가 많고, 대다수 아이템은 소비가 적음
    - 한 유저가 두 개의 아이템을 좋아하더라도 인기도가 높은 아이템의 점수가 높음 > 추천 시스템의 문제 > 알고리즘을 개선해보자
- 즉, 데이터의 인기 편향이 추천 시스템의 예측의 인기 편향을 야기함

#### 해결
- 손실함수(BPR Loss)에 `제어항` 추가
    - BPR Loss는 (Pos 점수) > (Neg 점수)로 학습하지만, Pos 아이템 간에 점수차를 없애지는 않음
    - Pos 아이템의 점수차를 줄이는 제어항 추가

### 주관적 추천시스템 연구 방향
- 추천시스템 연구
    - ML/DL 모델 고도화, 정확도 향상
    - 경영, 사용자, 도메인 특수성 고려 (추천 시스템의 특성상)
        - ML적 관점에서 인기 편향은 큰 문제가 아님 But, 추천 시스템에서는 문제가 있다!
- 유사한 연구
    - 상호작용, 대화형 추천시스템
        - 추천 시스템이 추천해주는 아이템에 대한 피드백을 다시 활용한다
        - 지도학습에서 학습된 패턴에서 벗어난 취향을 다시 탐구할 수 있음
        - '사용자'가 주도하는 추천
    - 뉴스 추천시스템
        - 정치적으로 너무 한쪽에 치우쳐지지 않도록 언론사의 방향성에 맞는 추천 제공이 필요함

<br/>
<br/>

## 대조학습을 통한 콘텐츠 기반 음악 추천에서의 비선호도 반영
Exploiting Negative Preference in Content-based Music Recommendation with Contrastive Learning  
박민주 (서울대 융합과학기술대학원 지능정보융합학과)  

### 연구 동기
- 개인화된 음악 추천을 위해선 음악 취향을 이해해야 함
    - '어떤 음악을 싫어하는 가'에 대한 답변이 더 쉽다
    - 비선호도 반영이 음악 추천을 향상할 수 있을까?
- 기존 컨텐츠 기반 음악 추천은 유사도에 의존한다
    - 아이템 콘텐츠와 더불어 `사용자 피드백`을 함께 반영해보자!
- 음악을 스킵하는 행동을 하나의 요소로 비선호도를 반영한 연구는 존재한다
    - 비선호도 요인의 특징적인 역할에 대한 설명을 해보자!

#### 연구 진행
1. Feature Extraction
    - 사전학습 된 모델 활용 (CLMR, MEE, Jukebox)
1. Contrastive Learning Exploiting Preference (CLEP)
    - Pair한 데이터를 받았을 때, 그 것이 같은 레이블이라면 (P-P or N-N) 가깝게, 다른 레이블이라면 멀리 위치하도록 훈련
    - 여기서 N-N Pair를 중점적으로 연구 (CLEP-N)
1. Preference Prediction
    - MLP 레이어를 통한 예측

#### 결과
- 음악 취향을 설명할 때, 비선호도는 어떤 역할을 할까?
    - CLEP-N은 가장 높은 정확도를 보임
    - 싫어하는 음악들이 좀 더 뚜렷한 특징을 가짐 (분포를 보았을 때)
    - Serendipity(의외로 취향인 음악)을 설명하기 좋음
- 비선호도 반영을 통해 음악 취향을 어떻게 향상 시킬 수 있을까?
    - CLEP-N은 가장 낮은 FPR과 가장 높은 Precision을 보임
    - false positive metric을 향상 > 취향이 아닌데 추천을 해주는 경우를 줄임

<br/>
<br/>

## RecSys 2022, 그 후기
이수진 (웅진씽크빅 데이터 분석가)

- Novelty (흥미로운 연구, 새로운 관점)
    - Federated Learning을 추천 시스템에 어떻게 연결할 수 있을까?

- Trends
    - GNN 모델
    - 강화학습
    - Explainable
        - 어떻게 이게 추천이 되었는가?
        - 그래프를 통한 설명
        - repsys 프레임워크 (https://github.com/cowjen01/repsys)