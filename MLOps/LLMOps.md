LLMOps

# LLMOps
Generative AI / Large Language Model 의 개발, 배포, 관리

#### Issue
1. 훈련 비용이 현재도 많이 들며, 더 정교한 모델을 만들기 위해서 비용이 가파르게 상승함
1. Fine-tuning이 어렵다
    - 모델을 유지하기 위해서 data pipeline 등과 같은 요소를 건강하게 유지해야 함
    - 이런 것들을 할 수 있는 회사가 많지 않음
1. LLMs hallucinate
    - LLM은 거짓말을 할 수 있음
    - 이는 data quality, generation method, input context 등으로 해결할 수 있음
1. 요청 규모가 방대할 경우, 응답 지연 시간도 문제가 될 수 있음 (Scale and latency)


## 구성
Reference: https://medium.com/p/6d45d1668aba

<img src="https://user-images.githubusercontent.com/40620421/261314261-74db2488-46b2-4a2e-85e1-9f458ba32d0d.png" width="500">

1. Black-box LLM APIs
    - ChatGPT가 제공하는 서비스 형태
    - Prompt를 통해서 주요 상호 작용
    - 최상의 응답을 얻기 위해서 Prompt를 잘 작성하는 Prompt Engineer가 생김
1. Enterprise Apps in LLM App Store
    - Enterprise App Provider에서 제공하는 데이터들을 통해서 모델을 추가로 훈련
1. Enterprise LLMOps — LLM fine-tuning
    - 기업에서 LLM을 활용하기 위해서는 기업의 데이터를 훈련해야함
    - 그를 위해서 자체 LLMOps 환경을 구축할 필요가 있음
    - 구성 요소
        - Data Source > Data Pipeline > Vector Stores
        - Open Source Pre-trained LLM
        - Context-specific LLM
        - LLM API
        - Model Versioning, Model Caching, Model Monitoring
1. Multi-agent LLM Orchestration
    - LLMOps의 미래 (Auto GPT)
    - 여러 AI 앱을 조합하여 새로운 Enterprise AI 앱을 개발
        - 판매, 여행 계획, 주문 등을 실행

### 구성 요소
<img src="https://user-images.githubusercontent.com/40620421/261539172-e0e23fea-9c0a-42d6-b8ae-88a6f9930ecb.png" width="500">

#### 1. Client-side Orchestration
- 최종 사용자 상호작용을 용이하게 하는 데 도움
- 배포된 모델 API와 연결
- Prompt Managing에 도움
    - 출력과 결과가 최대한 일치하도록 질문을 조정
    - 쉽게 사용할 수 있으며, 유연성을 가진다면 더 좋음
    - 평가, 디버깅, 버저닝, 생애주기 관리 등의 기능도 필요
- 예시. LangChain
```py
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage, SystemMessage, AIMessage

# System: AI에게 해야할 일을 알려주는 배경 컨텍스트
# Human: 사용자 메시지
# AI: AI가 응답한 내용
chat(
[ 
    SystemMessage(content="당신은 사용자가 짧은 문장으로 어디로 여행할지 알아낼 수 있도록 도와주는 멋진 AI 봇입니다."),
    HumanMessage(content="해변이 좋은데 어디로 가면 좋을까요?"), 
    AIMessage(content="프랑스 니스에 가야 해요"), 
    HumanMessage(content="그곳에 가면 또 뭘 하면 좋을까요?")
]
)

# >> AIMessage(content='니스에 머무는 동안 매력적인 구시가지를 둘러보고, 유명한 프롬나드 데 앵글레를 방문하고, 맛있는 프랑스 요리를 맛볼 수도 있습니다.', additional_kwargs={})
```

#### 2. Vector/Data Management
- LLM을 개발하기 위해서는 정보에서 임베딩을 생성하는 것이 필요
    - 모델 연산 과정에서 벡터의 유사성을 계산
- 문서/정보에서는 수천개의 Token이 생성될 수 있음
- 이를 효율적으로 저장하고, 검색하기 위해서 Vector Store가 필요함


#### 3. Experimentation
- Training, Fine tuning, inference 과정은 비용이 많이 소모됨 또한 응답 시간이 오래 걸리면 해당 Model을 채택하기 어려움
- 만약 Fine tuning을 잘못하면 과적합, Hallucination 등이 발생할 수 있음
- 따라서 이를 효과적으로 실험하고, 평가하는 도구가 필요함


#### 4. Server side Orchestration
- Deployment
    - Model Architecture 확장, 모델 최신 버전 유지, 모델 간 전환하는 작업 필요
    - On-Demand GPU 인프라도 필요
    - Deployment Pipeline 자동화를 통해서 Train, fine-tuning, inference 간소화를 할 수 있음
    - 사용자 중단 최소화, 업그레이드, 롤백 기능도 필요
- Observability
    - Production 단게에서 코드 관찰, 평가, 최적화 및 디버깅이 중요
    - LLM 모니터링의 문제
        - 모델이 성능 측정이 어려움 > 사용자 상호작용을 분석할 수 있도록 해야함
        - 블랙박스 모델의 경우, 아키텍처나 train-dataset을 확인할 수 있기 때문에 더욱 어려움 > 새로운 테스트 및 비교 프레임워크가 필요
    - 모델 드리프트를 감지하고, 새로운 데이터로 모델을 업데이트 해야 함
- Privacy
    - GDPR, CCPA, HIPAA 등과 같은 엄격한 개인 정보 보안 법이 있음
    - 모델 공정성, 편향, 독성 (안전하지 않거나 혐오스러운 콘텐츠 생성)을 평가하는 도구 필요
    - Prompt Injection, 데이터 유출, 유해한 언어 생성으로부터 보호할 수 있는 제품 필요