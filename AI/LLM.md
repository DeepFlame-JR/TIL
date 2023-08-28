LLM

# LLM (Large Language Model)
- 텍스트 생성, 자연어 이해, 기계 번역 등 다양한 자연어 처리(Natural Language Processing, NLP) 작업을 수행
- 방대한 양의 텍스트 데이터를 기반으로 사전 훈련된 후, 특정 작업에 맞게 추가적인 훈련을 거침


## 자연어 처리(Natural Language Processing, NLP)
- 인간의 언어를 기계가 이해하고 처리할 수 있도록 하는 분야
    - 텍스트 데이터를 이해, 분석, 생성 및 해석하는 기술
    - 인간과 기계 간의 언어 커뮤니케이션을 가능하게 함
- 주요 작업
    1. 텍스트 분류
        - 주어진 텍스트를 사전 정의된 카테고리로 분류하는 작업 
        - 예. 스팸 메일 필터링, 감성 분석, 문서 분류 등
    1. 개체명 인식
        - 텍스트에서 중요한 개체(인물, 장소, 날짜 등)를 인식하고 분류하는 작업
        - 예. "Apple은 캘리포니아에 위치한 기업입니다"라는 문장에서 "Apple"을 조직명으로 인식
    1. 기계 번역
        - 한 언어로 작성된 문장을 다른 언어로 자동으로 번역하는 작업
        - 예. 구글 번역과 같은 온라인 번역 도구가 이 작업에 사용
    1. 자동 요약
        - 중요한 내용이나 주제를 추출하여 요약을 생성
    1. 질의 응답
        - 질문에 대한 정확한 답변을 생성하는 작업
- 대표 모델
    1. BERT 
        - 구글에서 개발한 사전 훈련된 언어 모델로, 양방향 Transformer 인코더를 기반
        - 문맥을 이해하는 데 중점을 두고 있으며, 문장의 좌우 문맥을 함께 고려하여 단어의 의미를 파악
        - 텍스트 분류, 개체명 인식, 질의 응답 등 다양한 NLP 작업에 사용
    1. GPT
        - OpenAI에서 개발된 사전 훈련된 언어 모델로, 텍스트 생성에 특화
        - 훈련 데이터에 기반하여 다음 단어를 예측하거나, 주어진 문장을 이어서 완성하는 등의 작업을 수행
    1. Transformer
        - 인코더와 디코더라는 두 개의 주요 구성 요소로 구성
        - self-attention(자기 어텐션) 메커니즘을 사용하여 문장의 단어 간 상호 작용을 모델링하며, 기계 번역과 같은 다양한 NLP 작업에 사용
        - BERT와 GPT는 Transformer 아키텍처를 기반

#### Scaling Laws for NLM (Neural Language Models)
- 모델의 크기나 매개변수 수가 증가함에 따라 모델의 성능이 어떻게 변하는지를 설명하는 법칙
- LM의 성능은 아래에 의존함
    - 모델 파라미터 수 (N)
    - 데이터셋의 사이즈 (D)
    - 컴퓨터링량 (C)


### Masked language model
- 언어 모델링의 한 유형으로, 주어진 문장에서 일부 단어를 마스크하고, 마스크된 단어를 예측하는 모델
- 동작 방식
    - 입력 문장에 마스크
    - 양방향 학습 (문맥을 더 잘 이해하고 더 정확한 예측)
    - Fine-tuning (특정한 자연어 처리 작업(예: 문장 분류, 질의응답, 기계 번역 등)을 위함)

### 사전훈련을 위한 목적함수
- Next Sentence Prediction
- Shuffle Token Detection / Random Token Substitution
- Translation Language Model (다른 두 언어가 한쌍으로 토큰이 마스킹되어 사용)
- Span Boundary Objective (문장에서 연속된 토큰을 마스킹하여 토큰 예측)
- Sentence Order Prediction (연속된 두 문장을 추출하여 순서를 바꾸고, 이를 맞추는 지 확인)

### tokenization
- 텍스트를 작은 단위인 토큰(Token)으로 나누는 과정
    - 텍스트를 처리하고 분석하는 과정이 훨씬 용이해지며, 모델이 텍스트를 이해하고 처리하는데에도 도움
    - 텍스트 데이터를 숫자로 표현해야 하는 자연어 처리 모델에서 중요한 과정
    - 단어 간의 상관관계를 파악하고 텍스트의 의미를 추론하는 작업을 수행
    - 오타가 있을 때도 sub-word를 살릴 수 있음 (postfix, prefix도 검출 가능)
- 예시
    - "Natural language processing is interesting!"
        - Natural
        - language
        - processing
        - is
        - interesting
        - !

### seq2seq
- 입력된 시퀀스로부터 다른 도메인의 시퀀스를 출력하는 다양한 분야에서 사용되는 모델
    - 예. 챗봇 (입력 시퀀스와 출력 시퀀스를 각각 질문과 대답으로 구성), 번역기
- 구조
    - input(embedding) > Encoder > Context Vector > Decoder > output
        - embedding: 단어를 벡터의 형태로 표시
        - Context Vector: 인코더가 입력 문장의 모든 단어들을 순차적으로 입력받은 뒤에 마지막에 이 모든 단어 정보들을 압축해서 만든 하나의 벡터 (고차원인 모델은 해당 요소의 차원이 큼)
        - Decoder: Context Vector를 받아서 하나씩 단어를 뱉음
            - 1번 단어: <sos> + Context Vector
            - 2번 단어: 1번 단어 + 첫번째 은닉 상태
            - 3번 단어: 2번 단어 + 두번째 은닉 상태
    - 은닉상태: 입력 시퀀스의 각 위치에서 얻은 중간 결과. 현재까지의 입력정보를 요약하는 벡터

<img src="https://user-images.githubusercontent.com/40620421/258954985-f5565184-f93f-4a5f-8922-03aea9a67431.png" width="500">

- 훈련

```py
from tensorflow.keras.layers import Input, LSTM, Embedding, Dense
from tensorflow.keras.models import Model
import numpy as np

# Encoder
encoder_inputs = Input(shape=(None, src_vocab_size))
encoder_lstm = LSTM(units=256, return_state=True)

encoder_outputs, state_h, state_c = encoder_lstm(encoder_inputs)
# LSTM은 바닐라 RNN과는 달리 상태가 두 개. 은닉 상태와 셀 상태.
encoder_states = [state_h, state_c]

# Decoder
decoder_inputs = Input(shape=(None, tar_vocab_size))
decoder_lstm = LSTM(units=256, return_sequences=True, return_state=True)

# 디코더에게 인코더의 은닉 상태, 셀 상태를 전달.
decoder_outputs, _, _= decoder_lstm(decoder_inputs, initial_state=encoder_states)

decoder_softmax_layer = Dense(tar_vocab_size, activation='softmax')
decoder_outputs = decoder_softmax_layer(decoder_outputs)

model = Model([encoder_inputs, decoder_inputs], decoder_outputs)
model.compile(optimizer="rmsprop", loss="categorical_crossentropy")

model.fit(x=[encoder_input, decoder_input], y=decoder_target, batch_size=64, epochs=40, validation_split=0.2)
```


### Attention Mechanism
- 신경망들의 성능을 높이기 위한 매커니즘. Transformer의 기반이 됨
    - Decoder에서 출력 단어를 예측하는 매 시점마다 Encoder의 전체 입력 문장을 다시 한번 참고
    - 동일한 비율 X. 해당 시점에서 예측해야할 단어와 연관이 있는 입력 단어에 더 집중
    - 기존 seq2seq의 문제점
        1. 하나의 고정된 크기의 벡터에 모든 정보를 압축하려고 하니 정보 손실 발생
        1. RNN의 고질적 문제인 기울기 소실 문제가 존재 (GRU, LSTM에서도 같은 문제)
- Attention 함수
    - Attention(Q, K, V) = Attention Value (dictionary 형태)
        - Q(Query): t 시점의 디코더 셀에서의 은닉 상태
        - K(Keys): 모든 시점의 인코더 셀의 은닉 상태들
        - V(Values): 모든 시점의 인코더 셀의 은닉 상태들
    - Query에 대해서 모든 Key와의 유사도를 각각 구함 > Value에 구한 유사도를 반영해서 리턴 
    - Attention Score를 어떻게 구하느냐에 따라서 Attention 종류가 달라짐
- Dot-Product Attention
    - Decoder가 예측을 할 때, Encoder의 문장을 다시 참고 
    - 기존에는 t-1 은닉상태와 t-1의 출력 단어를 입력받음 + 여기에 t시점의 Attention Value 추가
    - 과정
        1. Attention Score를 구한다
            - 인코더의 모든 은닉 상태 각각이 디코더의 현 시점 은닉 상태와 얼마나 유사한지 판단하는 스코어 값
            - 인코더 은닉 상태와 디코더 현 시점 은닉 상태의 내적
        1. 소프트맥스 함수를 통해 어텐션 분포를 구한다
            - Attention Score를 합이 1이되는 확률분포로 변환
            - 예. 0.1, 0.4, 0.1, 0.4 가중치가 나타남
        1. 각 인코더의 어텐션 가중치와 은닉 상태를 가중합하여 Attention Value를 구한다
            - 각 인코더의 은닉 상태와 가중치 값들을 곱하고 더함
            - 인코더의 문맥을 포함하고 있다고 해서, Context Vector라고도 불림
            
            <img src="https://user-images.githubusercontent.com/40620421/258975229-8c1ca5d0-3cac-4e7c-a6ee-7a1c6787e5dd.png">

        4. Attention Value와 디코더의 t 시점의 은닉 상태를 연결한다
            <img src="https://user-images.githubusercontent.com/40620421/258955033-1f6956a3-7189-4180-afb9-46963738fee3.png" width="500">



### Transformer
- 주로 자연어 처리(Natural Language Processing, NLP) 작업에 사용되는 모델
    - seq2seq 모델은 입력 시퀀스를 하나의 벡터 표현으로 압축 (정보 일부 손실)
    - 이를 해결하기 위해서 Attention Mechanism 활용
    - 만약 Attention만으로 Encoder와 Decoder를 만든다면?
- Encoders와 Decoders로 구성
    - Encoders: Input > Positional Encoding > Multi-head Self-Attention > Encoder Result
    - Decoders: Input > Positional Encoding > Masked Multi-head Self-Attention > Multi-head Attention (+Encoder Result) > Dense > Softmax

    <img src="https://user-images.githubusercontent.com/40620421/258854357-a67dbf69-b9f8-45ad-b63d-9e84aa2afe5e.png" width="500">

- 핵심 요소
    - Query, Key & Value
        - layer의 Input 값
        - 예시
            - Youtube에서 검색을 한다고 했을 때,
            - Query: 검색어
            - Key: 결과로 나타나는 비디오의 제목
            - Value: Key 비디오 내에 있는 콘텐츠
        - 따라서 Query 와 유사한 Key를 찾을 필요가 있음
            - cos 함수를 통해서 Similarity를 계산
            - Similarity(Q, K)
    - Positional Encoding
        - Transformer에서는 모든 임베딩을 한번에 수행하기 때문에 빠름
            - But, 정보 손실이 우려됨
        - sin, cos 파동을 통해서 각 단어의 유니크한 포지션을 포함하여 임베딩
            - 순서대로 임베딩하는 것보다 빠르면서 순서도 포함할 수 있는 형태로 임베딩하는 방법
    - Query, Key & Value 활용

        <img src="https://user-images.githubusercontent.com/40620421/261799819-f52bd75f-c9c2-4234-b3c8-b7fec3761961.png" width="500">

        - 왜 똑같은 Key, Value 값이 중복되서 사용되는 것일까?
        - 우선 Query와 Key의 유사성을 찾는다 (MatMul > Scale > Softmax : Attention Filter)
        - 유사성에다가 원래 값인 Value를 곱함 > 불필요한 값들은 희미해짐



- 어텐션 메커니즘(Attention Mechanism)
    1. Encoder Self-Attention
    1. Masked Decoder Self-Attention
    1. Encoder-Decoder Attention
- Encoder
    - num_layers 개수의 인코더 층을 쌓음
    - 한 개의 층은 Self_Attention의 병렬적 구조 (Multi-head Self-Attention) + Position-wise FFNN (완전 연결 FFNN) 으로 구성
    - Self-Attention
        - Attention을 스스로 실행 (Q, K, V가 모두 같음 / 입력 문장의 모든 단어 벡터들)
        - The animal didn't cross the street because it was too tired. (animal, street, it이 it과 연관성이 크게 나타남)
        - Q, K, V 벡터는 Model의 Dimension/num_heads
            - 예. 512차원의 모델에서 num_heads가 8이면 > 벡터 크기는 64
    - Multi-head Attention
        - 여러 번의 Attention을 병렬로 사용하는 것이 효과적임
        <img src="https://user-images.githubusercontent.com/40620421/259108859-3b5c8d99-ea5b-4368-ab31-1cb2438db3c9.png" width="500">
- Decoder
    - 첫번째 서브층: Query = Key = Value
    - 두번째 서브층↑: Query: 디코더 행렬 / Key = Value: 인코더 행렬

## Training

### Pre-training
- 모델을 초기화하기 위해 대규모 텍스트 데이터로 모델을 훈련시키는 과정
    - 많은 양의 텍스트 데이터(인터넷 문서, 책, 뉴스 등)를 사용하여 모델은 문장 구조, 어휘, 문맥 등과 같은 언어의 다양한 특징을 파악하고 내재된 표현을 학습
    - 사전 훈련된 모델은 후속 작업을 위해 미세 조정(Fine-tuning) 단계로 이어질 수 있음
- 목적
    - 언어 이해의 일반화
        - 모델이 언어의 다양한 패턴, 구조, 문법, 단어 간 관계 등을 이해하는 일반화된 표현을 학습
        - 모델은 다양한 언어 작업에 적용될 수 있는 범용적인 언어 이해 능력을 갖출 수 있음
    - 데이터 희소성 극복
        - 많은 문장과 단어 조합이 가능한데, 각각의 조합에 대한 레이블이 부족하거나 없을 수 있음
    - 전이 학습
        - 사전 훈련된 모델은 일반 언어 이해 능력을 보유하고 있기 때문에, 특정 작업에 대한 미세 조정을 통해 해당 작업에 특화된 표현과 패턴을 학습
        - 데이터 양이 적은 작업이더라도 효과적인 학습
- 한계
    - knowledge cutoff
        - 훈련된 내용만 알고있음
        - 예. 2021년까지 정보로 훈련된 모델은 2023년에 일어난 일을 모름
    - 같은 질문에 같은 대답을 함

### Fine-tuning
- 사전 훈련된 모델을 특정 작업에 맞게 추가로 훈련시키는 과정
- 목적
    - 작업에 맞는 특화된 표현 학습
        - 특정 작업에 대한 레이블이 있는 작은 규모의 작업 관련 데이터를 사용하여 모델을 추가로 훈련
    - 작은 데이터셋에서의 효과적인 학습
        - 작은 데이터셋이나 특정 도메인에 특화된 작업에서도 효과적인 학습을 가능
        일반적인 언어 이해 능력을 기반으로 작은 데이터셋에서도 성능을 높일 수 있음
    - Overfitting 방지와 일반화 능력 향상
        - 특정 작업에 초점을 맞추기 때문에, 모델이 작업 관련 데이터에 과적합되지 않도록 제어

<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*LTfNOqZQB_M42KyvttjSyw.png" width="500">

#### 예시
BERT를 기반으로 한 감성 분석 작업

1. 사전 훈련
    - BERT 모델은 대규모 텍스트 데이터를 사용하여 사전 훈련됨
    - BERT는 언어의 일반적인 특성을 학습하고 문장의 의미와 문맥을 이해하는 능력을 개발
1. Fine-tuning 데이터 수집
    - 감성 분석을 위한 작업 관련 데이터를 수집
    - 문장 또는 문서와 그에 대응하는 감성(긍정 또는 부정) 레이블로 구성
1. Fine-tuning
    - BERT 모델의 일부 레이어 또는 분류기를 수정하여 작업에 특화된 표현을 학습
    - 예를 들어, 출력 레이어를 감성 분류를 위한 이진 분류기로 교체하고, 해당 분류기를 훈련 데이터에 맞게 조정
1. 성능 평가
    - 테스트 데이터를 사용하여 모델의 성능을 평가하고 정확도, 정밀도, 재현율 등의 평가 지표를 계산


### Parameter Efficient Fine Tuning (PEFT) 
- 대부분의 파라미터를 프리징하고 일부의 파라미터만 파인튜닝
    - 사전 학습된 LLM에 데이터셋을 활용해서 파인튜닝하는 것으로 성능향상을 할 수 있음
    - 기존의 사전 학습된 언어 모델을 추가 학습 없이, 주어진 평가 데이터에 적용하여 평가 점수를 얻을 수 있도록 도움
    - 허깅페이스의 transformers, accelerate 라이브러리와 연동해서 활용할 수 있음
- 단계 
    1. 사전 학습된 언어 모델 선택
    1. 언어 모델에 대한 점수 기록
    1. 불공정한 비교 보정 (점수를 보다 정확하게 평가)
- 종류
    - Prompt modifications ("Hard" prompt tuning, "Soft" prompt tuning, Prefix-tuning)
    - Adapter methods (LLaMA-Adapter)
    - Reparameterization (LoRA / Low rank adaption)

### Reinforcement Learning from Human Feedback (RLHF)
- 사용자의 Query를 통해서 훈련을 진행
    - 사실상 LLM의 마지막 훈련단계
    - Simple binary cross-entropy loss를 활용해서 프로세스를 단순화할 것을 제안
- Direct Preference Optimization (DPO)
    - Reward Modeling step을 우회하고, 핵심 통찰력을 통해 선호도 데이터에서 언어 모델을 최적화
    - Direct likelihood objective가 reward model을 활용하지 않도록 개선


### RAG (Retrieval-Augmented Generation)
- Retrieval 기능을 LLM 텍스트 생성에 통합
    - 대규모 자료에서 관련 문서를 가져오는 retrieval system과 답변을 생성하는 LLM을 결합
    - 모델이 외부 정보를 조회하여 응답을 개선하도록 도움
    - 보통 vector store를 이용 (관련 문서를 찾는 데에 도움)
- 보통 LLM을 당장 기업에서 도입하기 가장 손쉬운 방법

<img src="https://user-images.githubusercontent.com/40620421/264033743-239e52ec-17ec-4377-a33b-93d8c6dab2b8.png" width="500">


## Evaluation

### GLUE Benchmark
- 자연어 처리(NLP) 분야의 다양한 태스크들에 대한 표준화된 평가를 제공하기 위해 개발된 데이터셋과 벤치마크
    - 대표적인 NLP 태스크들을 모아서 구성되어 있으며, 다양한 언어 이해 작업에 대한 성능을 비교하고 평가
    - 총 9개의 Task로 되어 있음. train/val/test set이 주어짐
- NLP 태스크
    - MNLI(MultiNLI): 문장 페어 분류 태스크로, 주어진 두 문장 간의 관계를 예측하는 작업
    - QQP(Quora Question Pairs): 문장 페어 분류 태스크로, Quora의 질문 쌍에서 같은 의미의 질문 쌍을 예측하는 작업
    - SST-2(Sentiment Analysis): 문장 분류 태스크로, 영화 리뷰에서 감정(긍정/부정)을 예측하는 작업
    - CoLA(Corpus of Linguistic Acceptability): 문장 분류 태스크로, 주어진 문장이 문법적으로 올바른지를 예측하는 작업
    - MRPC(Microsoft Research Paraphrase Corpus): 문장 페어 분류 태스크로, 주어진 두 문장 간의 유사성을 예측하는 작업
    - RTE(Recognizing Textual Entailment): 문장 페어 분류 태스크로, 주어진 두 문장 간의 함의(Entailment) 여부를 예측하는 작업
    - WNLI(WIkiText-103-NLI): 문장 페어 분류 태스크로, 주어진 두 문장 간의 관계를 예측하는 작업


## LLM Fine-tuning 기법
https://velog.io/@nellcome/Instruction-Tuning%EC%9D%B4%EB%9E%80


### Prompt
- 주어진 작업을 인공지능 모델에게 지시하거나 원하는 결과를 얻기 위해 사용되는 문장 또는 텍스트 조각
    - 모델이 원하는 방식으로 작업을 수행하도록 지시하고, 사용자와의 상호작용을 시작하는데에 중요한 역할
    - 프롬프트의 선택과 설계는 모델의 동작과 결과에 큰 영향
- 구성
    - 지시어(Instruction)
        - 프롬프트는 작업을 수행하기 위한 지시어를 포함
        - 예를 들어 "번역해주세요."나 "질문에 답해주세요."와 같이 모델에게 수행할 작업을 명시적으로 지시하는 내용이 포함
    - 입력(Starting Input)
        - 일부 경우, 프롬프트에는 시작 입력(Starting Input)이 포함
        - 시작 입력은 모델의 답변을 시작하는데 사용되는 기본적인 문장 또는 텍스트 조각
    - 문맥(Context)
        - 대화 시스템에서는 이전 대화 내용을 포함하여 프롬프트를 구성하는 경우가 많음
        - 이전 대화의 문맥이 현재 프롬프트에 포함


### In-Conext Learning
- 대화식 AI 모델에서 사용되는 학습 방법 중 하나로, 모델이 대화의 문맥(context)을 고려하여 지속적으로 학습하고 개선하는 방식
    - 사용자와의 상호작용을 통해 모델이 실시간으로 학습하도록 돕는 데에 중요한 역할
    - 대규모 언어 모델에서 효과적
- 실제 사용자와의 상호작용을 통해 모델이 지속적으로 학습하고 개선하는 기술
    - 기존의 기계 학습 모델은 미리 정해진 대용량의 정적인 데이터셋을 사용하여 학습하는 방식. 이러한 한계를 극복하기 위해 개발된 방법. 
- 장점
    - 실시간 학습: 모델이 실제 사용자와 대화하면서 학습하기 때문에, 최신 정보와 상황에 적절하게 대응
    - 개인화: 사용자와의 상호작용을 통해 모델이 사용자에게 맞춤화된 답변을 생성하고 개선
    - 데이터 편향 감소: In-Context Learning은 실제 사용자와의 상호작용을 통해 다양한 데이터를 수집하므로 데이터 편향을 감소


### Instruction Tuning
- 다중 작업(feintuning)을 포함하는 대화형 AI 모델을 개선하기 위해 사용되는 학습 방법
    - user의 의도는 다른 답변을 주는 문제점을 해결
    - 기존의 대화형 AI 모델은 다양한 작업에 대해 미리 학습된 모델을 사용하여 다양한 요청에 대응 > 사용자의 의도를 제대로 이해를 못 할 수도 있음 > `사용자의 지시사항을 포함한 추가적인 지시 사항을 모델에 전달하고, 모델이 이러한 지시를 따르도록 강조적으로 학습`
- 과정
    1. 데이터셋 준비
        - Instruction Tuning은 사용자의 요구사항에 대한 지시사항이 포함된 데이터셋을 사용하여 모델을 학습
        - 이 데이터셋은 더 정확하고 명확한 지시를 제공하며, 모델이 원하는 대답을 생성하도록 도움
    1. Multi-task Finetuning
        - Instruction Tuning은 다중 작업(fineturning)을 활용하여 모델을 학습
        - 여러 작업에 대해 동시에 학습하면서, 특히 지시사항을 따르도록 강조하여 모델을 개선
    1. 지시 사항 강조
        - 지시 사항이 포함된 대화 형식으로 모델을 학습시키면서, 지시 사항을 더 잘 이해하고 지켜지도록 학습하도록 유도
