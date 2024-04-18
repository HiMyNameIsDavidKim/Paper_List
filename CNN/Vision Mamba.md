# Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model
* ZHU, Lianghui, et al. Vision mamba: Efficient visual representation learning with bidirectional state space model. arXiv preprint arXiv:2401.09417, 2024.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 최근 효율적인 디자인의 state space model(SSM) Mamba가 긴 시퀸스 모델링에 큰 가능성을 보여줬다.
* 하지만 SSM 에게도 비전 문제는 어려운 문제다.
* 비주얼 데이터의 위치 민감도와 이미지 전체 맥락에 대한 이해가 필요하다.
* 고해상도 이미지 처리가 가능한 고효율 비전 맘바 모델을 제시한다.
<br><br>

### [기존 문제점]
* Transformer는 속도와 메모리 문제가 크다.
* Mamba를 사용하려고 해도 2가지 문제점이 있다.
    * unidirectional modeling
    * 포지션에 대한 인식력 부족
<br><br>

### [해결 아이디어]
* 2가지 특징 결합한 모델 제안
    * bidirectional SSM : 데이터를 근거로 이미지 전반의 context를 모델링한다.
    * position embedding : 위치를 인식해 비주얼 리코그니션을 한다.
    * Vision Mamba, Vim
* 배경 지식
    * structured state space sequence models (S4) and Mamba
    * 이 두 모델은 continuous system의 discrete 버전이라고 할 수 있다.
    * 타임스케일 파라미터 Δ가 연속형 파라미터 A, B를 이산형 파라미터 A바, B바로 만든다.
    * 여기서 사용하는 메서드가 zero order hold(ZOH), (2)이다.
    * (1)식을 타임스케일 Δ에서 (3)으로 나타낼 수 있다.
    * 마지막으로 글로벌 합성곱을 통해 (4)으로 나타낼 수 있다.
    * 여기서 M은 인풋 시퀸스 x의 길이 이고, K는 커널이다.
* Vision Mamba
    * 스탠다드 맘바는 1차원 시퀸스 인풋으로 디자인 되어 있다.
    * 비전에서 사용하기 위해 2차원 이미지를 플래튼된 2차원 패치로 만든다.
    * 각 패치를 벡터 스페이스에 리니어 프로젝션 한다.
    * ViT와 BERT처럼 전체 시퀸스를 대변하는 클래스 토큰을 추가한다.
    * 포지션 임베딩을 더해준다.
    * 시퀸스를 수학적으로 (5)로 나타낸다. (t: 패치, W: 학습된 프로젝션 매트릭스)
    * 이를 Vim 인코더 블럭에 넣는다.
    * 노멀라이즈 와 MLP 헤드를 거쳐 최종 결과를 얻는다.
* Vim block
    * 비전 task를 위한 bidirectional sequence modeling을 했다.
    * 노멀리제이션 뒤 리니어 프로젝션. dim 사이즈에 맞게 차원을 키워 x, z로 만든다.
    * x는 포워드 방향, 백워드 방향 2가지로 진행된다.
    * 1차원 Conv -> 리니어 프로젝션. B, C Δ 3개로 만들어준다.
    * Δ는 A와 B를 A바와 B바로 만드는데 사용한다.
    * A바와 B바는 SSM을 사용해 y_forward와 y_backward를 계산한다.
    * z를 게이트로 사용하여 y_forward와 y_backward를 선택적으로 추출.(RNN methods)
    * 두 결과를 더하면 최종 아웃풋 시퀸스 T_l.
* Architecture details
    * L(블럭 수), D(히든 스테이트 디멘션), E(익스펜디드 스테이트 디멘션), N(SSM 디멘션)
    * 스탠다드 모델, L=24, D=192, E=384, N=16
    * 16x16 패치, dim=384 인 DeiT와 최대한 비슷하게 만든게 스탠다드 모델.
* 효율성 분석
    * SSM 메서드는 푸리에 트랜스폼을 가속화하여 빨리 계산한다. (Eq 4)
    * 알고리즘의 11번째 줄에 의하면 SSM은 효율적이지 못하다.
    * 이런 문제를 해결하기 위해 하드웨어 친화적인 방법을 사용한다.
    * 핵심 아이디어는 GPU의 IO 바운드와 메모리 바운드를 피하는 것이다.
    * IO Efficiency
        * high bandwidth memory(HBM)과 SRAM이 중요하다.
        * 보통 SRAM은 larger bandwidth, HBM은 bigger memory를 갖는다.
        * HBM을 사용한 Vim의 SSM 연산은 O(BMEN)차 메모리 수를 요구한다.
        * Mamba에서 사용한 방법을 차용하여 HBM이 아니라 더 빠른 SRAM을 사용한다.
        * SRAM을 사용하면 SSM 연산은 O(BME+EN)차 메모리 수를 요구한다.
        * SRAM의 연산이 끝나면 이를 HBM으로 전달한다.
        * 더 빠른 SRAM을 사용했고, 연산에 사용된 메모리 수도 줄었다.
    * Memory Efficiency
        * 긴 시퀸스가 들어오면 메모리가 부족해 멈추는 일이 많다.
        * 이는 근본적으로 모든 양의 데이터를 '동시'에 계산해야 하기 때문이다.
        * Vim은 Mamba에서 사용한 recomputation method를 차용한다.
        * backward에서 gradient를 계산하려면 활성화 값을 계산해야 한다.
        * 이 활성화 값은 저장할 경우 메모리를 크게 차지하지만, 다시 계산하면 빠르게 얻어낼 수 있는 특징을 가지고 있다.
        * 따라서 'recomputation', '재계산' 전략을 이용해 메모리를 절약한다.
    * Computation Efficiency
        * 셀프 어텐션 레이어와 SSM의 계산 복잡도는 (7), (8) 이다.
        * 셀프 어텐션 레이어는 시퀸스 길이 M에 대하여 2차함수 이다.
        * SSM은 시퀸스 길이 M에 대하여 1차함수 이다.
<br><br>

### [결과 분석]
* Classification
    * 디테일 : ImageNet 1K, input size=224, (랜덤 크롭, 랜덤 플립, 라벨 스무딩, 믹스업), AdamW, cosine annealing, batch size=1024, epochs=300, lr=1e-03
    * 롱 시퀸스 fine-tuning
        * 기존의 패치 사이즈 224/4 = 56 에서 8까지 대폭 줄여서 훨씬 긴 시퀸스를 만들고 여기에 30 에포크 학습.
        * 성능 최적화. 더 복잡한 디테일을 캡쳐하는 능력 향상.
    * 결과
        * CNN과 비교, 비슷한 사이즈 Res50과 Vim-S 4.3% 높다.
        * ViT의 최적화 모델인 DeiT와 비교, Ti와 S 모두 높다.
        * SSM 기반 모델인 S4ND-ViT와 비교, 크기는 3배 적지만 비슷한 성능.
        * 롱 시퀸스 fine-tuning 하면 더 좋아진다.
        * FPS와 GPU 메모리 사용량 비교, 일단 Vim의 성능이 더 좋은데 이미지 사이즈가 커지면 커질수록 격차가 벌어진다.
        * 더 긴 시퀸스 일수록 메모리와 연산을 덜 사용하면서도 성능은 좋다.
* Semantic segmentation
    * 디테일 : ADE20K, UperNet framework
    * 결과
        * DeiT와 비교, Ti와 S 모두 높다.
        * CNN과 비교, 큰 사이즈의 ResNet101과 같은 성능.
        * 다운 스트림을 효율적으로 평가하기 위해 FPN 모듈 적용.
        * 기존 백본들에 비해 이미지가 커질수록 효율성 격차가 크다.
* Object detection, Instance segmentation
    * 디테일 : COCO 2017, ViTDet framework
    * 결과
        * DeiT와 비교, AP box와 AP mask 모두 좋은 성능.
* Ablation study
    * Bidirectional SSM 빼면 성능이 드랍된다.
    * 다만 SSM과 Conv1D 결합하여 사용해야만 한다.
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* SSM forward and backward
<br><br>


