# Alphazzle: Jigsaw Puzzle Solver with Deep Monte-Carlo Tree Search
* PAUMARD, Marie-Morgane; TABIA, Hedi; PICARD, David. Alphazzle: Jigsaw Puzzle Solver with Deep Monte-Carlo Tree Search. arXiv preprint arXiv:2302.00384, 2023.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* RL은 복잡한 보드 게임 같은 문제에서 뛰어난 성능을 보인다.
* 직소 퍼즐에 RL을 적용해보자.
* 직소 퍼즐 reassembly 전략으로 Monte Carlo Tree Search (MCTS) 알고리즘을 사용해보자.
<br><br>

### [기존 문제점]
* 직소 퍼즐 reassembly 전략으로 brute force 알고리즘을 사용한다.
<br><br>

### [해결 아이디어]
* Alphazzle overview
    * 딥마인드의 알파고 후속작인 AlphaZero를 사용한다.
    * Alphazzle은 3가지 특징을 가진다.
        * single-player games에 최적화 한다.
        * CNN이 이미지를 보고 reward를 추정한다.
        * reward는 플레이 도중에 추론하는 것이 불가능하다.
    * 마지막 특징은 고고학의 실제 작업 (중간 결과가 얼마나 맞춘건지 모름)에서 영감을 얻었다.
* Simplified framework for two-player games
    * 두 에이전트 g_1과 g_2가 턴제 게임을 한다.
    * s_0: 초기 보드 state
    * s_t: t번째 턴 (현재) state
    * s_t_max: 최종 게임에서 state
    * r: scoring function, reward 계산, reward는 {-1, 0, 1} 중 1개
    * 각 턴 t에서 에이전트 한명이 상대방의 최종 이득을 최소화하는 선택을 추정하고 실행한다.
    * g_1은 reward를 최대화 하는 것을 목표로 하고, g_2는 최소화 하는 것을 목표로 한다.
    * 다음 action 선택은 policy π_θ에 기반한다.
    * P: policy를 학습하는 네트워크. 다음 action을 선택한다.
    * 일부 게임은 시간이 너무 오래걸릴 수 있으므로 value funtion이 필요하다.
    * v: value function, 현재 상태에서 예상되는 reward의 합이다.
    * V: reward 예측을 학습하는 네트워크. policy의 최적화를 유도한다.
* AlphaZero algorithm
    * 알파제로는 MCTS를 계획 알고리즘으로 사용하여 action을 선택한다.
    * MCTS는 policy π_MCTS를 리턴한다.
* Jigsaw puzzle rules and formalization
    * Fig 2
    * 초기 state가 제공된다. (배치해야할 퍼즐 조각들 + 지금까지 배치한 결과)
    * MCTS가 시뮬레이션을 통해 case 별로 reward를 계산한다.
    * action을 선택하고, 이번 state를 업데이트한다.
    * end game까지 이를 반복한다.
    * reward를 위해 3가지 matric을 사용한다.
    * 올바른 이웃의 비율, 올바르게 배치된 패치의 비율, 퍼즐 완성 여부 3가지 이다.
* Monte Carlo Tree Search
    * Fig 3
    * MCTS는 현재 state s_t에서 N_visit 노드를 방문한다.
    * 노드에서 가장 유망한 액션 a_t를 리턴한다.
    * 다음 state s_t+1으로 넘어가 게임이 끝날 때까지 반복한다.
    * N_visit 노드를 방문하면 policy π_MCTS도 리턴한다.
    * MCTS는 방문 시 4단계 (Selection, Expansion, Simulation, Backpropagation)를 진행한다.
    * 그림을 보면 s_t에서 노드마다 reward 예상값 V를 계산한다.
* Deep Reinforcement Learning
    * Pre-training P
        * policy를 학습하는 네트워크.
        * (이미 재조립된 이미지)와 (새 퍼즐 조각)이 주어지면 best action을 예측한다.
        * classification task로 CE를 사용한다.
    * Pre-training V
        * reward 예측을 학습하는 네트워크.
        * 재조립이 완료되거나, 조각이 1개 남았을 때 reward를 예측한다.
        * V가 Eq. 3를 예측하도록 훈련한다.
        * V는 MSE를 사용한다.
    * Architectures
        * P의 backbone으로 WideResNet(WRN)을 사용한다.
        * (이미 재조립된 이미지)와 (새 퍼즐 조각)의 feature 추출을 하며, weight를 공유한다.
        * 두 값은 각자 MLP(dim=512) 레이어 6개를 통과한다.
        * 결과를 concat하고 FC 레이어 2개를 통과하고 softmax 한다.
        * V의 backbone도 WRN을 사용한다.
        * reward 값을 예측하도록 수정해서 사용한다.
<br><br>

### [결과 분석]
* Table 4
    * fragment-level, puzzle-level 정확도.
    * MCTS를 사용한 경우와 P 혹은 V에 Greedy 알고리즘을 사용한 경우 비교.
* Table 5
    * permutation 9!에 대한 puzzle-level 정확도 다른 논문과 비교.
    * fragment-level은 75.1%, puzzle-level은 51.5% 이다.
    * permutation 제한하는 방법은 구현하지 못했으며, Deepzzle 보다 나은 결과이다.
    * Deepzzle 보다 계산 비용과 시간이 덜 들었다. (값 없음)
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


