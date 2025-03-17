# LLaMA: Open and Efficient Foundation Language Models
* TOUVRON, Hugo, et al. Llama: Open and efficient foundation language models. arXiv preprint arXiv:2302.13971, 2023.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 7B ~ 65B 크기의 foundation language 모델을 만든다.
* 독점적이고 비공개된 데이터가 아니라 오픈 데이터로 SOTA를 달성해보자.
* 저자들은 리서치 커뮤니티에 모델의 모든 정보를 공개했다.
<br><br>

### [기존 문제점]
* 요즘 LLM 연구들은 모델 사이즈가 성능에 비례한다는 가정을 가진다.
* 기존 모델들은 학습 예산만 고려하고 추론 예산에 대하여 고려하지 않는다.
* 친칠라, PaLM, GPT-3는 본인들만 접근할 수 있는 클로즈 데이터셋을 사용한다.
<br><br>

### [해결 아이디어]
* 1T 학습 토큰 모델과 학습 예산
    * approach에 앞서 친칠라 논문을 먼저 분석해본다.
    * 더 작은 사이즈의 모델도 더 많은 데이터로 학습하면 성능이 좋다. 
    * `학습 예산`에서 모델 사이즈와 데이터셋 사이즈의 최적의 조합을 찾는다.
    * 저자들은 이 논문이 `추론 예산`를 무시하고 있다고 말한다.
    * 성능의 타겟 레벨을 고정했을 때, 학습이 빠른 모델보다 추론이 빠른 모델이 더 좋다.
    * 비록 학습이 빠른 모델은 연구에서 비용이 싸지만, 추론이 빠른 모델이 서비스에서 더 싸다.
    * 서비스에서 더 싼 것이 궁극적으로 더 유리할 수 있다.
    * 이 관점에서 기존 모델보다 더 많은 학습 토큰으로 훈련해보자.
    * 학습 토큰 수를 늘린 모델으로 다양한 `추론 예산`에서 최상의 성능 모델을 훈련한다.
* Overview
    * 대규모 데이터셋에 대하여 대규모 트랜스포머를 기존 옵티마이저를 사용해 학습한다.
* Pre-training Data
    * 오픈소스 데이터셋만 사용했고 7종을 섞어서 사용했다.
    * CC: CCNet의 소스 데이터셋으로 크롤링 데이터셋, 영어 외 제거, 저퀄 제거
    * C4: 다른 크롤링 데이터셋, CommonCrawl을 다양하게 넣을수록 성능이 좋아지므로 사용
    * Github: 깃허브 데이터셋, 구글 빅쿼리로 접근 가능
    * Wikipedia: 20개 국어의 위키피디아 데이터셋, 라틴어 키릴어 계열 20개 언어
    * Gutenberg, Books3: 구텐베르크 프로젝트와 ThePile의 Books3 섹션 데이터셋
    * ArXiv: 아카이브 레이텍 파일 데이터셋, 과학 데이터셋 추가 용도
    * Stack Exchange: 스택 익스체인지 사이트 자료 데이터셋, 다양한 도메인의 질문과 답변
    * 토크나이저: 서브워드 기법인 BPE (Byte Pair Encoding) 사용
    * Table 1
    * 토크나이제이션이 끝나면 약 1.4T 크기의 토큰이 포함된다.
    * 에포크 수를 보면 위키피디아와 Books 2개 빼고 나머지는 단 1번만 학습에 사용된다.
* Architecture
    * 트랜스포머 아키텍처에 다양한 개선 메서드를 적용했다.
    * Pre-normalization
        * GPT-3에서 영감을 얻은 메서드이다.
        * 아웃풋 시 norm을 하는게 아니라 인풋 전에 norm을 한다.
        * 학습의 안정성을 개선하는 효과가 있다.
        * norm은 RMSNorm을 사용한다.
    * SwiGLU
        * PaLM에서 영감을 얻은 메서드이다.
        * 활성화 함수 ReLU를 SwiGLU로 모두 대체한다.
        * 성능이 향상되는 효과가 있다.
    * Rotary Embeddings
        * GPTNeo에서 영감을 얻은 메서드이다.
        * 기존의 absolute 포지션 임베딩을 rotary 포지션 임베딩으로 대체한다.
* Optimizer
    * AdamW 인데 일반적으로 쓰는 β_2인 0.999 보다 낮은 0.95를 사용한다.
    * 최근 기울기의 영향력을 더 높인 것이다.
    * 학습 속도가 더 빨라지지만 안정성이 감소할 수 있다.
    * cosine learning rate from 100% to 10%
    * 2000 warm-up
* 효율성 개선
    * causal multi-head attention (=Masked MHSA)을 사용한다.
    * 디코더 기반 모델이 주로 사용하는 것으로 어텐션 웨이트를 저장할 필요 없다.
    * 백워드 마다 다시 계산해야하는 활성화 값을 저장해두고 사용한다.
    * 각 GPU는 활성화 값 계산과 GPU 간 소통을 순차적이 아닌 동시에 진행한다.
    * 2048대의 A100으로 21일이면 학습할 수 있다.
<br><br>

### [결과 분석]
* Main results
    * Zero-shot: task에 대한 설명과 테스트 예제 입력, 개방형 생성 or 답변 순위 출력
    * Few-shot: 몇몇의 예제와 정답과 테스트 예제 입력, 개방형 생성 or 답변 순위 출력
    * Open-ended generation: 개방형 생성, AI의 자유로운 답변
    * Ranking proposed answers: 답변 순위, 여러 가능한 답변 중에 순위 매기기
    * GPT-3, Gopher, Chinchilla, PaLM 등과 비교했다.
    * Common Sense Reasoning
        * Table 3
        * BoolQ, PIQA, SIQA, HellaSwag, WinoGrande, ARC easy and challenge, OBQA
        * Cloze task: 빈칸 맞추기 문제
        * Winograd task: 문장 내 대명사 맞추기 문제
        * PaLM과 비교했을 때 크기는 작은데 Winograd 외에 모든 평가에서 성능이 좋다.
        * 특이한 점은 ARC는 65B 보다 13B 에서 더 좋은 성능을 보인다.
    * Closed-book Question Answering
        * Table 4, 5
        * NaturalQuestions, TriviaQA
        * 질문을 답할 수 있는 근거를 포함한 문서를 접근하지 않고 대답
        * 65B가 0 shot, few shot 평가에서 SOTA를 달성했다.
        * 13B 크기만 해도 기존 모델과 견줄만한 성능이다.
        * 특이한 점은 NaturalQuestions 0 shot에서 33B가 성능이 더 좋다.
    *  Reading Comprehension
        * Table 6
        * RACE
        * 중국 학생들의 영어 교육에 사용되는 독해 문제 자료
        * 65B 모델이 PaLM-540B 수준이며, 13B 모델이 GPT-3 보다 성능이 좋다.
    * Mathematical reasoning
        * Table 7
        * MATH, GSM8k
        * MATH: 레이텍으로 작성된 중고등학교 수학 문제 12k
        * GSM8k: 중학교 수학 문제 세트
        * maj1@k 방법: k개의 답변을 생성하고 그 중 가장 빈도가 높은 답변 선택
        * Minerva는 PaLM을 수학 데이터셋으로 fine-tuning 한 모델이다.
        * 65B 모델은 파인튜닝을 하지 않았지만 비슷한 크기의 Minerva-62B 보다 좋은 성능이다.
        * 하지만 SOTA를 달성하지 못했으며 PaLM 보다도 낮은 성능이다.
    * Code generation
        * Table 8
        * HumanEval, MBPP
        * 프로그램에 대한 설명 제공 후 코딩, 인풋-아웃풋 예제로 코드 평가
        * HumanEval의 @1을 제외하면 모든 테스트에서 가장 좋은 성능을 보인다.
        * PaLM-cont 모델은 기존보다 더 길게 학습한 모델인데, 비슷한 크기의 LLaMA의 성능이 더 좋다.
        * 모든 모델은 코드 특화 토큰으로 fine-tuning을 더 하면 성능이 올라간다.
    * Massive Multitask Language Understanding (MMLU)
        * Table 9
        * 인문학, 공학, 사회과학 등 다양한 도메인에 대한 다중 정답 문제 평가
        * 
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 일부 벤치마크에서 SOTA 성능이 나오지 않았는데 65B 보다 큰 모델을 만들지 않은 이유는 뭘까
* 
<br><br>



## [`메모`]

### [배경 지식]
* 
<br><br>


