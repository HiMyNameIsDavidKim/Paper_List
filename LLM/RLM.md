# Recursive Language Models
* ZHANG, Alex L.; KRASKA, Tim; KHATTAB, Omar. Recursive language models. arXiv preprint arXiv:2512.24601, 2025.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 무제한 길이의 프롬프트를 처리할 수 있도록 해보자.
* inference-time scaling 관점 개선
    * RLM은 프롬프트를 외부 환경처럼 취급하고 프로그램적으로 접근할 수 있게 한다.
    * LLM이 탐색하고 분해하며 재귀적으로 호출하여 프롬프트를 처리할 수 있게 한다.
    * 100배 이상의 프롬프트도 성공적으로 처리 가능
    * 짧은 프롬프트 에서도 바닐라 모델에 비해 좋은 성능
    * 비용은 비슷한 수준
* LLM에 2가지 도구를 제공해보자. (파이썬 REPL, 재귀 호출)
<br><br>

### [기존 문제점]
* context rot
    * 프론티어 모델들은 프롬프트의 길이에 제한이 있고 그 안에서도 context rot 현상이 있다.
    * Fig 1
    * context rot: 프롬프트의 길이가 길어질수록 성능이 떨어지는 현상
    * LLM의 발전에 따라 context rot은 개선될거라 예상하지만 개선방법이 필요한게 현실이다.
    * LLM이 long-horizon task를 수행하기 시작하며 문제는 더 심각해지고 있다.
* condensation, compaction
    * 기존 프롬프트를 과도하게 압축하여 디테일에 손상이 간다.
    * 정보가 계속 많아지면 새 컨텐츠를 넣기위해 이전 컨텐츠를 제거하는 현상이 발생한다.
<br><br>

### [해결 아이디어]
* RLM 개요
    * inference-time scaling을 사용한다.
    * 저자들은 이 방법을 reasoning 기법에서 영감을 얻었다.
    * 어떤 모델이든 범용적으로 사용할 수 있는 추론 패러다임이다.
    * 핵심 아이디어는 무한하게 긴 유저 프롬프트를 네트워크에 직접 넣지 않는 것이다.
    * 대신 유저 프롬프트를 외부 환경처럼 취급하게 한다.
    * 그리고 symbolically, recursively 상호작용하게 한다.
    * symbolically: 프로그래밍 코드나 변수를 사용
    * recursively: 모델이 자기 자신을 재귀적으로 호출
    * Fig 2
    * RLM은 기존 LLM과 동일한 인터페이스 (인풋, 아웃풋)을 가진다.
    * 질의응답 프로세스
        * 프롬프트 P가 인풋으로 주어진다.
        * REPL(Read-Eval-Print Loop) 즉 대화형 프로그래밍 환경을 구현한다.
        * P를 변수값으로 세팅한다.
        * LLM에게 일반적인 컨텍스트를 제공한다. (ex. P의 길이 등)
        * 코드를 작성하여 프롬프트를 탐색하고 decompose할 권한을 준다.
        * 코드 실행에 대한 side effect가 생기지 않는지 감시한다.
    * RLM은 LLM이 입력 프롬프트를 이해하고, 변환하고, 코드를 실행하게 돕는다.
    * 그리고 이 과정에서 symbolic 프로그램을 작성하고 스스로를 호출한다.
    * RLM은 코딩, 검색, sub-agent 등에서 부족한 expressive power를 보완한다.
    * expressive power: 기존에 비해 더 복잡하고 긴 작업
    * 기존 에이전트는 주어진 LLM 컨텍스트 윈도우 만큼만 스니펫, 청크 등을 사용할 수 있다.
* RLM 알고리즘 및 특징
    * M: 기본 LLM 모델
    * K: 최대 컨텍스트 사이즈
    * RLM은 M을 감싸는 inference-time scaffold (추론 시점 발판) 이다.
    * 유저 프롬프트를 외부 환경으로 취급하고 M을 여러번 나누어 호출한다.
    * 결론적으로 전체 내용을 빠짐없이 꼼꼼하게 처리할 능력을 챙길 수 있다.
    * Algo 1 (RLM 알고리즘)
    * RLM의 핵심 알고리즘
    * P: 임의 길이의 프롬프트
    * E: 영구적인 외부 환경
    * 우리는 모델이 무제한의 입력, 출력, 의미론적 작업을 감당하길 원한다.
    * 1. REPL 환경 초기화 (P 사용)
    * 2. sub-RLM을 호출할 수 있는 함수 추가
    * 3. RLM 루프 실행
        * 최초:
            * root 모델 M 호출, 고정된 크기의 메타데이터 수집 (길이, 짧은 앞부분 등)
        * 이후:
            * REPL에서 코드를 실행
            * state (상태)를 업데이트
            * stdout (표준 출력)으로 출력 수집
            * 출력 내용에 대한 메타데이터를 History에 추가
            * Final 변수 설정 시 loop 탈출 후 최종 응답 리턴
    * root 모델은 RLM을 구동하는 시스템 프롬프트를 준다. (Appendix C)
    * 시스템 프롬프트: sub-RLM으로 P를 이해하고 변환하고 중간과정 변수 생성을 수행하도록 설계
    * Algo 2 (기존 알고리즘)
    * RLM은 기존 스캐폴드들에 없는 3개의 디자인을 사용한다.
    * Algo 2는 이 3가지 디자인이 빠진 기존의 스캐폴드들에 대한 디자인이다.
    * 입력 관리 방식
        * Algo 1은 root 모델에게 직접 복사한 텍스트를 제공하지 않는다.
        * Algo 2는 직접 복사한 텍스트를 넣어서 이미 컨텍스트 윈도우를 넘긴채로 시작한다.
    * 출력 생성 매커니즘
        * Algo 2는 root 모델이 autoregressive하게 직접 출력을 생성하게 한다.
        * 하지만 이 방식은 컨텍스트 윈도우를 넘어서는 크기의 출력을 낼 수 없다.
    * 재귀의 성질
        * Algo 2는 verbalized (말로 표현 가능한) 작업만 sub-LLM에게 위임한다.
        * Algo 1은 symbolic (코드, 변수 저장 등) 작업을 sub-LLM에게 위임한다.
<br><br>

### [결과 분석]
* Tasks
    * 프론티어 모델들은 RULER 벤치마크의 1M이 넘는 NIAH task도 잘 풀어낸다.
    * 하지만 여전히 1M보다 한참 작은 OOLONG 벤치마크에서 저조한 성능을 보인다.
    * S-NIAH: single NIAH, RULER 중 1개, 긴 텍스트에서 특정 문구 찾기, O(1)
    * BrowseComp-Plus: 1K개 문서의 흩어진 정보 조합, multi-hop 질의응답
    * OOLONG: 입력의 거의 모든 줄을 이해해야하는 높은 난이도 추론, O(N)
    * OOLONG-Pairs: 입력의 모든 쌍관계를 이해해야하는 높은 난이도 추론, O(N^2)
    * LongBench-v2 CodeQA: 대규모 코드 repo 내 여러 파일 로직 이해 추론, 객관식
* Baselines
    * GPT-5, Qwen3-Coder-480B-A35B, Qwen3-8B 모델 사용
    * 모델 추론 서비스 Fireworks 사용
    * CodeAct (+BM25): 코드 실행과 BM25 키워드 검색 기능을 갖춘 표준 에이전트, 프롬프트를 변수로 빼지 않고 모델에 직접 입력
    * CodeAct with sub-calls: 긴 프롬프트를 외부 변수가 아닌 모델 내부에 억지로 밀어 넣는 방식
    * Summary agent: 읽어들인 텍스트가 모델의 용량을 채울 때마다 내용을 요약하는 방식
* RLMs
    * RLM with REPL: 파이썬 REPL 환경, sub-RLM 재귀 호출
    * RLM with REPL, no sub-calls: 파이썬 REPL 환경만 사용
    * Finetuning: 480B-A35B의 실행 1000개를 8B 모델에 파인튜닝
* 결과
    * Table 1
    * 10M 이상의 토큰 처리 가능, 기존 프레임워크 대비 성능 우위
        * BrowseComp-Plus는 컨텍스트 윈도우를 넘어섰음에도 정답을 맞춘다. (6~11M)
        * GPT-5 mini에 모든 컨텍스트를 입력하는 것보다 GPT-5 RLM의 비용이 더 싸다.
        * 요약 기반의 Summary agent, 검색 기반의 CodeAct 보다 좋은 성능을 보인다.
        * 난이도가 O(N^2)인 OOLONG-Pairs에서 특히 성능 차이가 크다.
    * REPL의 효과, 재귀호출의 효과
        * 재귀호출 없이 REPL만 해도 기존 에이전트 프레임워크의 한계를 뛰어넘었다.
        * 재귀호출까지 사용하는 경우 더 좋은 성능을 보인다.
    * RLM의 trajectory 분산
        * RLM은 작업의 복잡도에 따라 반복 횟수(길이)에 큰 차이가 발생한다.
        * Fig 7, 8
        * RLM의 경우 특히 시간 소요의 분산이 매우 컸다.
        * 즉 적지 않은 확률로 추론 궤적을 멀리 벗어나는 리스크가 있다는 것이다.
    * RLM은 GPT-5에서만 특히 좋을지도?
        * GPT-5와 Qwen3 모두 다른 에이전트 스캐폴드보다는 RLM이 좋았다.
        * 하지만 Qwen3에 비해 GPT-5가 특히 RLM의 성능이 좋다.
        * RLM 프롬프트는 모든 모델에서 고정하여 사용하지 않는다.
        * 즉 프롬프트 엔지니어링이 필요한 부분이 존재한다.
        * (ex. Qwen3가 하위 호출을 너무 많이 실행해서 이를 방지하는 프롬프트 추가함.)
    * RLM 파인튜닝
        * 한 도메인에서 RLM 실행 trajectory를 활용하면 파인튜닝이 가능하다.
* 한계
    * 속도: RLM은 시퀀셜하게 실행되고 비동기 실행은 고려하지 않았다.
    * 재귀 깊이: RLM은 최대 재귀 깊이를 1으로 제한하여 오직 1 sub call만 허용한다.
    * RLM 궤적: RLM 궤적을 또 하나의 리즈닝 형태로 보면 새로운 학습이 가능하다.
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* RULER
    * long context LLM 평가 벤치마크
<br><br>
