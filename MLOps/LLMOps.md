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


## Vector Database
- Vector 형태의 데이터의 저장소
    - Image, Audio, Text 등을 모델이 이해하기 위해서는 데이터를 임베딩할 필요가 있음
    - 데이터의 양이 방대해져서 전문적인 저장소가 필요해짐
- LLM과의 연관성
    - 방대한 양의 데이터 수용하고, 효율적으로 처리할 수 잇음
    - 유사성 검색 및 매칭 기능 (모델이 훈련하는데 필요한 요소)
    - 다양한 형식의 데이터를 지원하며, 인덱싱 및 쿼리를 효과적으로 지원

<img src="https://user-images.githubusercontent.com/40620421/261784855-6f4acc24-3873-4b36-9e5c-f308e2c1536e.png" width="500">


## Serving
- 모델이 크기 때문에 복잡성이 증가함
    - 계산량 증가. 성능이 좋은 GPU를 사용해야 함 (A100, H100)
    - 메모리, 저장공간도 많이 필요
    - Scaling 비용도 많이 듬
    - Trade-off 가 필요함 (Cost, Accuracy, Latency, Throughput)
- 추가적으로 다양한 기능 필요
    - 배포 전략, Rollbacks, AB/Testing 기능
    - 정보 보호
    - 모델 버저닝

#### 이를 해결하기 위한 방법
1. Model Paralleization (병렬화)
    - 모델이 너무 커서 하나의 장치에 맞지 않을 때 주로 사용
        - 모델을 chunks/blocks로 나누고, GPU에 할당
        - 중간 결과 또는 활성화가 segment 간에 전달 됨
1. Token-wise model parallelism / Data paralleism
    - Input data batch가 클 경우 사용
        - 여러 장치에 모델을 복제한 후에 더 작게 쪼갠 token을 모델에 적용
        - 결과를 하나로 모아 조합
1. Pipeline-based parallelism
    - LLM을 분할하고, 각각은 파이프라인 방식으로 독립적으로 작동
    - 데이터는 서로 다른 segment 간에 순차적으로 흐름
1. Layer-based parallelism
    - LLM을 서로 다른 계층으로 분리 하여 각가의 장치에 할당
    - 각 계층/장치는 데이터를 독립적으로 처리
    - 각 프로세서의 메모리 사용량이 줄어듬

### 서빙 프레임워크

https://betterprogramming.pub/frameworks-for-serving-llms-60b7f7b23407

<img src="https://user-images.githubusercontent.com/40620421/259951949-dd10e312-22ba-4883-bf67-e99b734a13ba.png" width="500">

- vLLM: 속도가 가장 빠름
- DeepSpeed-MII: Letency가 가장 적음. DeepSpeed를 기존에 사용하고 있다면 추천
- Text Generation Inference(TGI): Hugging Face의 support 가능
- CTranslate2: CPU를 활용해서 서빙
- 비교 요소
    - latency: 어떤 작업을 처리하는데 걸리는 시간
    - throughput: 초당 처리하는 작업의 개수
        - Toekns per Second, Query per second
    - Quantization: 양자화 (파라미터를 lower bit으로 표현함으로써 access 속도를 높이는 기법)


### vLLM
- 속도가 가장 빠른 LLM 서빙 프레임워크
- 주요 기능
    - Continuous batching: iteration-level 스케줄링으로, 1개의 iteration에 쿼리를 쌓아서 전달. 많은 양의 쿼리를 한 번에 처리할 수 있도록 함
    - PagedAttention (https://vllm.ai/)
        - Input token이 key, value tensor를 만드는데, 이게 GPU 메모리를 많이 소모 (KV cache)
            - KV cache가 GPU 메모리에 보존되며, 병목현상을 일으킴
            - 길이에 따라 변동적이라서 많은 양의 메모리가 낭비됨
        - key-value를 비연속적으로 적재할 수 있도록 허락함
            - Block table을 통해서 가능하도록 함
            - 서로 다른 Sequence들이 Memory를 공유할 수 있도록 함
            - 더 많은 배치 가능, GPU 향상, 성능 향상

        <img src="https://user-images.githubusercontent.com/40620421/261799078-54d1b788-a65d-479d-83ff-822adc15b1ec.png" width="500">
- 장점
    - Text Generation 속도가 빠름
    - High-Throughput serving (parallel sampling, beam search 등의 알고리즘을 활용)
    - OpenAI-compatible APi server
- 한계
    - Custom model을 서빙할 수 있지만, 과정이 복잡
    - Adapter에 대한 지원이 부족 (LoRa, QLoRa 등)
    - 모델 양자화를 지원하지 않음

```py
# Offline Batched Inference
from vllm import LLM, SamplingParams

prompts = [
    "Funniest joke ever:",
    "The capital of France is",
    "The future of AI is",
]

# temperature: 응답의 무작위성 또는 창의성을 조절 (너무 높으면 모델이 관련 없는 응답을 생성할 수 있으며, 너무 낮으면 응답이 지나치게 로봇적일 수 있음)
# top_k: 가장 가능성이 높은 k개의 토큰을 반영
# top_p: 가장 가능성이 높은 최소 p개의 토큰을 반영
sampling_params = SamplingParams(temperature=0.95, top_p=0.95, top_k=0.95, max_tokens=200)
llm = LLM(model="huggyllama/llama-13b")
outputs = llm.generate(prompts, sampling_params)
```
```powershell
# API server
# Start the server
! python -m vllm.entrypoints.api_server --env MODEL_NAME=huggyllama/llama-13b

# Query the model in shell
curl http://localhost:8000/generate \
    -d '{
        "prompt": "Funniest joke ever:",
        "n": 1,
        "temperature": 0.95,
        "max_tokens": 200
    }'
```


### Text Generation Inference(TGI)
- HuggingFace에서 개발한 LLM 서빙 프레임워크
- 주요 기능
    - Prometheus Metrics가 내장되어 있어 성능 모니터링을 쉽게 할 수 있음
    - flash-attention, Paged Attention을 통한 Optimized Transformer code에 추론 진행
    - Sever-Sent Events(SSE)를 통한 Token streaming
        - SSE: 서버에서 생성된 업데이트 스트림을 구독하고, 이벤트가 발생할 때마다 클라이언트에 알림을 보내는 단방향 통신
        - Streaming: 답변 전체를 한번에 응답하는 것이 아니라, 답변을 나누어서 응답 (답변 생성이 오래걸린다면 이것도 하나의 방법)
    - 
- 이점
    - 모든 종속성이 Docker에 의해서 설치됨 > 확실하게 작동할 수 있음을 보장
    - HuggingFace Model Hub를 통해서 Model을 쉽게 서빙할 수 있음
    - 양자화, tensor parallelism 등의 다양한 옵션을 활용할 수 있음
- 한계
    - Adapter에 대한 지원이 부족 (LoRa, QLoRa 등)
    - Rust + CUDA kernel에서 컴파일되기 때문에 라이브러리 통합이 어려울 수 있음
    - 문서가 부족함 (Rust 언어를 활용하기 어려움)

```powershell
# Run web server
mkdir data
docker run --gpus all --shm-size 1g -p 8080:80 \
-v data:/data ghcr.io/huggingface/text-generation-inference:0.9 \
  --model-id huggyllama/llama-13b \
  --num-shard 1
```

```py
# Make queries
from text_generation import Client

client = Client("http://127.0.0.1:8080")
prompt = "Funniest joke ever:"
print(client.generate(prompt, max_new_tokens=17 temperature=0.95).generated_text)
```


## Monitoring 
- LLM 이용과 성능을 향상시키기 위해서 필요
    - 주로 사용자의 Prompt와 AI의 Response를 확인
- Consideration
    - Response Relevance: Input의 의도와 문맥에 벗어나는 정도를 모니터링
    - Sentiment: 사용자 Prompt의 감정적인 톤을 파악
    - Jalibreak Similarity: 모델이 제약 조건이나 규칙을 위반하여 컨텐츠를 생성하지는 않는지 모니터링
    - Topic: 사용자의 의도에 따라서 응답이 Topic에 머무는지 파악
    - Toxicity: 유해하거나 공격적인 부적절한 언어를 출력하는지 확인
- 종류
    - LangChain: 언어 모델을 구동하는 애플리케이션을 개발하기 위한 인기 있는 오픈 소스 프레임워크
    - LangKit: LLM 모델의 Prompt 및 응답을 사용하여 분석을 쉽게 추출
    - WhyLabs: 추가적인 인프라 설정없이 팀 간에 모니터링 구성 및 협업을 쉽게 함