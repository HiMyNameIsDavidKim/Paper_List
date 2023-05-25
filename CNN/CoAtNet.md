# CoAtNet: Marrying Convolution and Attention for All Data Sizes
* DAI, Zihang, et al. Coatnet: Marrying convolution and attention for all data sizes. Advances in Neural Information Processing Systems, 2021, 34: 3965-3977.
* NIPS 2021.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 효율적으로 CNN과 self-attention의 장점만 결합하기.
* Depth-wise Conv와 relative attention을 활용하면 두 구조를 합칠 수 있다.
* generalization, capacity, efficiency 3가지를 향상 시키자.
* JFT와 같은 추가적인 데이터셋 없이 SOTA 퍼포먼스를 달성해보자.
<br><br>

### [기존 문제점]
* ViT의 퍼포먼스는 작은 데이터셋에서 CNN을 넘지 못한다.
* 고성능을 위해서는 반드시 대형 데이터셋의 프리 트레이닝이 동반되어야 한다.
* 바닐라 ViT는 여전히 유용한 inductive bias가 부족하다.
<br><br>

### [해결 아이디어]
* Conv 레이어는 generalization이 더 빠르게 되는 특징.
* 어탠션 레이어는 capacity가 높아서 더 많은 dataset에서 유리한 특징.
* 3가지 특징 분석
    * Input-adaptive weighting
        * self-attention은 서로 멀리 떨어진 위치에 있더라도 복잡한 관계를 잘 캐치함.
        * 높은 수준의 개념을 처리하는데 가장 필요한 특징.
    * Translation equivariance
        * Conv의 weight는 주어진 포지션 사이의 상대적인 거리를 주로 고려함.
        * 크지 않은 데이터셋에 generalization 하는 것에 필요한 특징.
    * Global receptive field
        * self-attention은 receptive field가 사진 전체를 커버함.
        * 더 contextual한 정보를 제공하고, capacity를 높일 수 있음.
* Conv와 Self-attention 결합
    * 인버티드 보틀넥 구조를 사용하기 위해 MBConv 블럭을 사용함.
    * 상대적 거리를 고려하기 위해 relative attention을 사용함.
    * MBConv와 relative attention은 구조가 유사하며 결합하기 용이함.
    * 3가지 특장점을 모두 커버하는 모델 수립.
* Architecture
    * 다운 샘플링을 통해 spatial size를 줄이고, global relative attention 진행.
    * S0 : stem stage, (3x3) 커널을 사용해서 spatial size를 절반으로 줄이기.
    * S1 : Always MBConv block, Conv 블럭이 앞쪽 스테이지에 있는 것이 퍼포먼스에 유리함.
    * S2~4 : MBConv block or relative attention block.
    * 4가지 종류의 모델 (CCCC, CCCT, CCTT, CTTT) -> 실험 결과 CCTT가 베스트 모델.
* Generalization 분석
    * 예상한대로 Conv 레이어가 많을수록 generalization이 잘됨.
* Capacity 분석
    * attention 레이어가 많다고 capacity가 높은건 아님.
    * stem stage에서 ViT는 너무 많은 정보 손실을 당하고, capacity가 오히려 제한됨.  
<br><br>

### [추가로 볼 레퍼런스]
* MBConv Blocks
    * Mobilenetv2: Inverted residuals and linear bottlenecks
* relative attention
    * Self-Attention with Relative Position Representations
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* Depthwise Separable Convolution
    * 기존 Conv보다 빠르다.
    * 채널 방향의 연산을 하지 않기 때문.
    * (3D -> 1D) vs (2D -> 1D, 2D -> 1D) 연산량 약 1/9 감소.
* Inverted Residual Connection
    * Residual: wide -> narrow -> wide (wide layers are connected)
    * Inverted residual: narrow -> wide -> narrow (narrow layers are connected)
    * 압축된 양질의 정보가 저차원에서도 저장되어 있다는 가정.
* Relative attention
    * standard 어탠션 : 시퀸스 내에서 모든 토큰에 같은 웨이트 부여. 거리 상관하지 않음.
    * relative 어탠션 : 시퀸스 내에서 가까운 토큰일수록 더 큰 웨이트 부여.
    * 기존에 비해서 상대적인 위치를 교려한 어탠션 스코어를 계산함.
<br><br>


