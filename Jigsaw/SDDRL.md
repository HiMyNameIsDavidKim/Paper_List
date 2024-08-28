# Siamese-Discriminant Deep Reinforcement Learning for Solving Jigsaw Puzzles with Large Eroded Gaps
* SONG, Xingke, et al. Siamese-discriminant deep reinforcement learning for solving jigsaw puzzles with large eroded gaps. In: Proceedings of the AAAI Conference on Artificial Intelligence. 2023. p. 2303-2311.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 직소 퍼즐은 융합 연구 분야로, puzzle solving을 넘어 다른 테크닉 적용이 필요하다.
* Jigsaw Puzzles with Large Eroded Gaps (JPwLEG) 문제를 풀어보자.
* a Siamese-Discriminant Deep Reinforcement Learning (SDDRL) 모델을 제안한다.
* 2개의 SDN로 구성된 새로운 DQN을 디자인 했다.
    * SDN은 수직 조각 간의 쌍관계 인식, 수평 조각 간의 쌍관계 인식 역할
* DQN은 단일 조각에서 단서를 얻고, 4개의 이웃 조각에서도 단서를 얻는다.
* 모델 평가를 위한 JPwLEG 데이터셋 2개를 배포했다.
<br><br>

### [기존 문제점]
* 2스텝 (feature 추출, 퍼즐 reassembly)으로 진행한다.
* 기존 방법은 feature 추출을 지나치게 강조하고 reassembly 전략의 중요성을 간과한다.
* permutation 수가 늘어나면 치명적인 문제가 발생한다.
<br><br>

### [해결 아이디어]
* SDDRL overview
    * Fig 1
    * 섞인 위치의 조각 이미지들이 주어진다.
    * SDN 2개로 구성된 DQN이 수직 조각 간의 쌍관계와 수평 조각간의 쌍관계를 인식한다.
    * DQN은 퍼즐 조각 쌍의 스와핑 액션에 대한 value인 Q를 예측한다.
    * 리워드는 스와핑 액션을 실행한 뒤 올바르게 배치된 조각의 수로 계산한다.
    * greedy policy는 가장 큰 Q값을 가지는 액션이 무엇인지 찾는데 사용된다.
    * 사람의 전략과 비슷하게, 인접한 조각을 먼저 결합하고 점차 큰 조각으로 결합한다.
    * 기존 직소 퍼즐 메서드들과 다른점
        * 강화학습을 처음으로 사용한다.
        * center와 non-center의 관계가 아니라, 두 조각이 이웃일 가능성을 평가한다.
        * SDDRL은 조각에서 추출한 feature만 사용하는 것이 아니라, 과거 퍼즐 재조립 경험을 활용한다.
* Formulation of combinatorial optimization
    * 퍼즐 재조립 문제를 'combinatorial optimization' 문제로 공식화 했다.
    * Π는 퍼즐 조각의 가능한 모든 permutation 이다.
    * n은 퍼즐 조각의 개수이다.
    * π_0은 초기 permutation, π_G는 ground-truth permutation 이다.
    * 학습 목표는 π_G를 π_0으로 매핑하는 함수 M을 찾는 것이다.
    * center 조각이 고정되어 있으면 Π = (n-1)! 이다.
    * 최적의 매핑 함수 M은 모든 조각의 evidence E의 argmax로 구할 수 있다. Eq (2)
    * 주어진 조각 f_i에 대한 evidence는 E_i 이다.
    * 이웃하는 4개 조각의 supporting evidence를 고려해야 한다.
    * 현재 조각에 윗첨자 C를 쓰고, 상하좌우 이웃 조각에 윗첨자 (L, R, U, D)를 쓴다.
    * 모델은 총 5개의 evidence E_(C, L, R, U, D)를 추정한다.
    * pariwise 확률 4개(P_H 2개, P_V 2개)를 추정한다.
    * P_H는 수평 pairwise 확률, P_V는 수직 pairwise 확률 이다.
    * 최종 E_i는 Eq (3)으로 표현할 수 있다.
    * 이 공식은 E_C만 고려하는 Deepzzle과 많은 부분이 다르다.
    * 매핑 함수 M을 한번에 direct하게 찾는 것은 실현 불가능 하다.
    * 따라서 매핑 함수 M_t의 시퀀스를 구한다.
* Formulation of reinforcement learning
    * objective function은 Eq (2)로, E의 argmax를 구하는 것이다.
    * eroded gap이 크면 정확도가 많이 떨어진다.
    * E가 위치가 올바른지 아닌지에 대한 근사치만 제공하기 때문이다.
    * 이 문제를 해결하기 위해 E의 argmax를 구하는게 아니라 RL을 사용한다.
    * SDDRL은 ground-truth 위치를 사용해 정의된 reward의 최대를 찾는다.
    * 예를들어 올바른 위치에 놓으면 reward는 1, 아니면 0을 준다.
    * 이 경우에는 재조립된 결과에 대한 reward는 최대이지만, E는 최대가 아닐수도 있다.
    * 최대의 reward를 획득하는 것이 올바른 재조립 결과이므로 이 방법을 사용한다.
    * Markov Decision Process (MDP)를 다시 정의해 공식화 한다.
    * S (state space)
        * S의 구성요소 s_t는 퍼즐 조각의 정렬된 시퀀스 이다.
        * 상태를 인코딩하는 것에는 길이 n의 벡터 v가 사용된다.
        * 벡터 v_i_t는 타임 스텝 t에서 i번째 조각을 뜻한다.
    * A (action space)
        * A의 구성요소 a_t는 결정된 action space에서 action 이다.
        * F는 조각의 인덱스 집합이다.
        * f_c는 움직일 수 없는 센터 조각이다.
    * R (reward function)
        * R = αL_t + (1 − α)H_t + b + C_t 로 정의한다.
        * L_t는 정확한 위치에 배치한 조각의 수 이다.
        * H_t는 상대적 위치가 정확한 조각 쌍 이다.
        * C_t는 모든 조각을 모든 조각을 맞췄을 때 주는 큰 보상 이다.
        * b는 이번 스텝에서 모든 조각을 맞추지 못했을 때 주는 일정한 패널티 이다.
        * α는 L_t와 H_t의 중요성 균형을 맞추는 하이퍼 파라미터 이다.
    * P (probabilities of state transitions)
        * 이 문제에서 P는 deterministic process 이다.
    * γ (discount rate)
        * 에이전트는 타임 스텝 t 이후부터 누적된 reward의 최대화를 목표로 한다.
* SDN of DQN
    * DQN은 2개의 SDN으로 구성되어 있다.
    * SDN은 parwise 관계를 모델링하며, VGG-16으로 구성된다.
    * SDN은 두 조각이 이웃할 가능성을 판단한다.
    * VGG-16 외에 다른 백본도 써봤지만 가장 가볍고 성능이 좋았다.
    * 저자들은 조각이 너무 작기 때문에 VGG-16같은 단순한 구조가 더 general 하다고 설명한다.
* RL Strategy
    * Fig 2
    * SDDRL은 DQN 2개 (평가 DQN과 타겟 DQN)으로 구성된다.
    * Q는 기대 reward 값으로, R으로 계산된다.
    * 인풋은 현재 상태의 퍼즐 이미지이고, 아웃풋은 주어진 액션에 대한 Q 값 이다.
    * 평가 DQN은 수평 SDN, 수직 SDN 으로 수평 관계, 수직 관계를 포착한다.
    * 포착된 수평 관계, 수직 관계를 FC layer에 넣어 pairwise 확률을 Q 값에 매핑한다.
    * 두 조각에 대한 상대적 위치 혹은 절대적 위치를 올바르게 놓으면 보상이 커진다.
    * 절대적 위치가 더 중요하기 때문에 α는 1에 가깝다.
    * 패널티 b는 에이전트가 더 빠른 시간 안에 퍼즐을 풀도록 유도한다.
    * greedy policy a* 는 Q를 추정하여 현재 퍼즐의 배치를 평가하는데 사용된다.
    * 학습하는 동안 액션은 ϵ-greedy policy를 통해 결정된다.
    * 확률 ϵ에 의해 무작위 동작을 실행, 확률 (1-ϵ)에 의해 greedy 동작을 실행한다.
    * ϵ는 1로 시작해서 ϵ_min까지 점점 감소한다.
    * 완전한 정답 혹은 최대 스텝 T_max에 도달하면 스와핑 액션을 멈춘다.
    * T_max는 트레이닝에서 20,000 이며, 평가에서 50 이다.
    * W 이터레이션 마다 평가 DQN의 파라미터가 타겟 파라미터로 복사된다.
    * 수렴을 가속화하기 위해 priority replay mechanism 을 사용했다.
<br><br>

### [결과 분석]
* Construction of JPwLEG datasets
    * SDDRL을 평가하기 위해 JPwLEG 을 배포한다.
    * MET 데이터셋 (painting, engraving, artifacts) 12k 이미지를 선별했다.
    * 9k train, 2k val, 1k test로 나눈다.
    * 최종 퍼즐 조각의 사이즈는 항상 96 이다.
    * JPwLEG-3
    * 각 이미지를 398x398 사이즈로 리사이즈 한다.
    * (7+96+24) / (24+96+24) / (24+96+7) 사이즈 3x3으로 자른다.
    * JPwLEG-5
    * 각 이미지를 534x534 사이즈로 리사이즈 한다.
    * (3+96+6) / (6+96+6) 3개 / (6+96+3) 사이즈 5x5으로 자른다.
* Experimental Settings
    * Table 1
    * 센터 조각에 대한 relation 이면 -C, 이웃간의 pairwise relation 이면 -P 이다.
    * 3x3 퍼즐의 pairwise relation은  총 12개 (세로 6개 pair, 가로 6개 pair) 로 구성된다.
    * 예를 들어 세로 6개 pair는 (1, 4), (2, 5), (3, 6), (4, 7), (5, 8), (6, 9) 이다.
    * Perfect는 puzzle-level 정확도, Absolute는 fragment-level 정확도 이다.
    * 기존 모델들과 비교해서 가장 좋은 성능이었으며, -P에서 특히 좋았다.
    * Table 2
    * MET의 세부 그룹에 따른 정확도 구분.
    * Table 3
    * JPwLEG-5의 결과.
    * 
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* 
<br><br>


