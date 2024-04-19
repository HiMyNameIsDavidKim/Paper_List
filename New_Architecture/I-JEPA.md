# Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture
* ASSRAN, Mahmoud, et al. Self-supervised learning from images with a joint-embedding predictive architecture. In: Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023. p. 15619-15629.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* semantic image representations를 hand-crafted 데이터 없이 학습시켜보자.
* I-JEPA(Image based Joint Embedding Predictive Architecutre) 제시.
* 이미지 중에서 1개의 context block을 입력 받아 다른 여러개의 target block의 representations를 예측한다.
* masking 전략을 사용하여 2가지 이득을 취한다.
* 충분히 큰 스케일의 타겟 블럭. 즉 아웃풋 양이 많다.
* 충분히 공간적 분산 정보가 담긴 컨텍스트 블럭. 즉 인풋이 합리적이다.
<br><br>

### [기존 문제점]
* SSL 프리트레이닝은 invariance-based 와 generative 2가지로 나뉜다.
* invariance-based methods
    * 같은 이미지에 대하여 2개 이상의 다른 유사한 관점에 대한 임베딩을 추출한다.
    * 랜덤 스케일링, 랜덤 크로핑, 컬러 지터링 등.
    * 데이터 증강 기법은 강력한 바이어스를 야기하고, fine-tuning이 필요하거나 다른 종류의 data distribution에서는 새로 프리트레이닝을 해야한다.
* generative methods
    * cognitive learning 이론은 생물학적 시스템을 모방한 매커니즘으로, 생체 감각적인 반응을 예측하도록 한다.
    * 지우거나 손상된 일부분을 입력으로 받아 손상된 컨텐츠를 예측한다. (MAE)
    * 이 방법으로 학습된 결과 representation은 전형적으로 lower semantic level이며 linear probing으로 평가할 시 퍼포먼스가 떨어진다.
<br><br>

### [해결 아이디어]
* Architecture overview
    * 추상적인 representation 공간에서 지워진 정보를 예측한다.
    * 컨텍스트 블럭 1개가 주어지면, 다양한 타겟 블럭의 representation을 예측한다.
    * 이 작업은 1개의 같은 이미지 내에서 이루어진다.
    * 이 target representation은 이미 학습된 타겟 인코더가 계산한 값이다.
    * 기존의 generative methods는 pixel 공간에서 예측을 했다면, I-JEPA는 추상적인 공간에서 예측을 한다.
    * 불필요한 픽셀 레벨의 디테일을 굳이 예측할 필요 없고 고압축 의미론적 feature만 학습하고자 하는 것이다.
    * 멀티 블럭 마스킹 학습 전략도 중요하다.
    * SSL을 하면서도 충분히 많은 양의 타겟 블럭을 뽑아낼 수 있다.
* Background
    * EBM(Energy-Based Models)
        * EBM에서 아이디어를 차용했다.
        * EBM은 호환되지 않는 인풋 사이에는 높은 에너지를 어사인하고, 호환되는 인풋 사이에는 낮은 에너지를 어사인한다.
        * 기존의 SSL 방식들을 이 프레임워크로 변형한다.
    * JEA(Joint-Embedding Architectures)
        * JEA으로 invariance-based methods를 EBM화 한다.
        * 이미지 기반의 프리트레이닝은 호환되는 x,y 쌍을 가지고 있다.
        * 단지 인풋에 랜덤한 hand-craft 증강을 했을 뿐이다.
        * JEA의 차원 붕괴 관점의 문제는 에너지 면이 평평하다는 것이다.
    * GA(Generative Architectures)
        * GA로 generative methods를 EBM화 한다.
        * 호환되는 x에서 다이렉트로 y를 reconstruction 한다.
        * 디코더에서 추가적인 정보 z를 reconstruction에 활용한다.
        * MAE로 예를 들면 y를 복사하고 일부를 마스킹한 x를 인풋하며, 어떤 패치를 가렸는지에 대한 위치 정보와 그 마스크 자체를 디코더에서 z로 받는다.
        * GA는 차원 붕괴 관점에서 문제가 있지 않다.
    * JEPA(Joint-Embedding Predictive Architectures)
        * JEPA는 개념적으로는 GA와 유사하다.
        * 주요한 차이점은 인풋 스페이스가 아니라 임베딩 스페이스에서 loss를 계산하는 것이다.
        * JEPA는 호환가능한 x에서 임베딩을 추출하고 그걸로 y의 임베딩을 예측한다.
        * 디코더가 아닌 예측 네트워크를 사용한다.
        * I-JEPA는 이 아키텍쳐를 인스턴스하며, 이미지와 미스킹 기법을 사용한다.
        * JEA와 다른점은 추가적인 정보 z가 hand-craft 증강 기법이 아니다.
        * JEA와 유사한 원인으로 차원 붕괴의 문제를 야기할 수 있는데(에너지 면이 평평함), 이를 피하기 위해 x인코더 y인코더에 비대칭 아키텍처를 적용했다. (두 인코더 종류가 다름)
* Method
    * 저자들은 ViT 아키텍처를 활용해 context 인코더, target 인코더
<br><br>

### [결과 분석]
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


