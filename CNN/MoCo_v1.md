# Momentum Contrast for Unsupervised Visual Representation Learning
* HE, Kaiming, et al. Momentum contrast for unsupervised visual representation learning. In: Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 2020. p. 9729-9738.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 비지도학습에 특화된 크고 일관성 있는 딕셔너리를 구축해보자.
* visual representation learning에 Momentum Contrast 사용해보자.
<br><br>

### [기존 문제점]
* 이미지는 자연 그대로의 시그널이고 연속적이며 고차원적이다.
* 따라서 딕셔너리 구축이 힘들다. 새로운 딕셔너리 구축 방식이 필요하다.
* 기존 contrastive loss 방식으로는 크고 일관성 있는 딕셔너리를 구축하기에 여전히 부족하다.
<br><br>

### [해결 아이디어]
* contrastive loss
    * 이 학습 메서드는 다이나믹 딕셔너리를 구축하는 거라고 생각할 수 있다.
    * 딕셔너리는 키로 구성되는데, 이 키는 데이터에서 샘플링된다.
    * 비지도학습이 진행되면 인코더는 딕셔너리 룩업(사전 탐색)을 수행한다.
    * 딕셔너리 룩업이란 쿼리의 인코딩과 비슷한 키를 찾는 작업이다.
    * 이게 잘 되려면 인코딩 된 쿼리는 매칭할 키와 유사하고 다른 키와는 유사하면 안된다.
    * 그래서 학습이 진행되면서 contrastive loss가 최소가 되도록 한다.
    * contrastive loss는 두 개의 입력 데이터를 받아 유사한 데이터는 가까이 배치하고 다른 데이터는 멀리 배치하는 것을 공식화한 loss 이다.
* Overview
    * 크고 일관성 있는 딕셔너리를 구축하기 위한 새로운 전략 momentum contrast.
    * 딕셔너리를 큐로 유지한다. (현재 배치를 인큐, 가장 오래된 배치를 디큐)
    * 큐로 쌓기 때문에 미니배치로 설정한 사이즈 보다 커질 수 있다.
    * 키 인코더를 천천히 업데이트하여 일관성을 유지한다.
    * 뒤에서 더 자세히 설명.
* Contrastive learning as dictionary look-up
    * contrastive learning은 딕셔너리 탐색을 잘 하게 인코더를 학습하는 것이다.
    * q: 인코딩된 쿼리, k: 키(=인코딩된 샘플)
    * 여기서 쿼리는 단 1개의 키(k+)와 매칭된다고 가정하자.
    * 쿼리가 k+ 와 비슷해지고, 모든 다른 키와 달라지면 loss가 작아진다.
    * 연산이 내적(dot product)와 비슷하다.
    * 이 loss를 InfoNCE 라고 하며, 수식으로 나타내면 Eq 1과 같다.
    * τ: temperature, K: k+ 키 이외의 모든 키
    * 여기서 쿼리는 이미지 전체가 될 수도 있고 패치가 될 수도 있다.
* Momentum contrast
    * Dictionary as a queue
        * 딕셔너리를 데이터 샘플들의 큐로 유지한다. (계속 쌓는다)
        * 앞에 사용했던 미니배치의 인코딩된 키를 재사용할 수 있는 장점이 있다.
        * 보통 딕셔너리의 크기는 미니배치와 일치한다.
        * 하지만 큐를 사용하면 미니배치보다 더 큰 크기의 딕셔너리를 구성할 수 있다.
        * 크기가 유연하고 독립적인데 이를 하이퍼파라미터로 조절 가능하다.
        * 딕셔너리의 샘플들은 점진적으로 천천히 대체(업데이트) 된다.
        * 이번 배치는 딕셔너리에 인큐, 가장 오래된 배치는 딕셔너리에서 제거된다.
    * Momentum update
        * 큐 딕셔너리를 사용하면 단점이 있다.
        * back propagation으로 키 인코더를 업데이트할 수 없다.
        * naive한 솔루션은 쿼리 인코더를 복사해 키 인코더로 사용하는 것이다.
        * 그런데 이 솔루션은 실험적으로 안좋은 결과가 나왔다.
        * 저자들의 가설은 키 인코더가 너무 급격하게 변해서 키의 일관성이 떨어지는 문제가 발생했다는 것이다.
        * 그래서 이 문제를 해결하기 위해 모멘텀 업데이트를 제안했다.
        * 인코더의 파라미터를 θ라고 할 때 업데이트 식은 Eq 2와 같다.
        * m은 모멘텀을 사용할 비율, 모멘텀 계수 이다.
        * 오직 쿼리 인코더의 파라미터만 back propagation으로 업데이트 된다.
        * Eq 2가 키 인코더의 파라미터가 더 스무스하게 변하도록 해준다.
        * 실험적으로 모멘텀이 크면 클수록(천천히 스무스하게 업데이트) 좋았다.
    * Relations to previous mechanisms
        * 기존의 contrastive loss 모델 2개와 비교.
        * end to end 방식
            * Fig 2의 a.
            * 기본적인 매커니즘대로 back propagation으로 업데이트.
            * 현재 미니 배치를 딕셔너리로 사용.
            * 딕셔너리가 일관성은 있으나 크기가 작다.
            * 미니배치와 딕셔너리의 크기가 같으므로 GPU 크기의 영향을 받는다.
        * memory bank 방식
            * Fig 2의 b.
            * 모든 샘플들의 representation으로 memory bank를 구성한다.
            * 각 딕셔너리를 위한 미니배치는 메모리 뱅크에서 랜덤하게 샘플링된다.
            * 메모리 뱅크는 back propagation으로 업데이트 되지 않는다.
            * 전체 미니배치 중에서 마지막 미니배치를 업데이트에 사용한다.
            * 딕셔너리가 크기는 크지만 일관성이 떨어진다.
            * 메모리 뱅크는 전체 샘플을 다 포함하고 업데이트에 사용되는 샘플은 곧 자기 자신의 일부이므로 업데이트가 적절하게 되지 않기 때문이다.
            * 전체 미니배치를 다 써야하기 때문에 메모리에서 비효율적이다.
    * Pretext task
        * 기존의 pretext task 중에서 하나 채택했다.
        * 같은 이미지에서 만든 쿼리와 키를 postivie pair로 설정했다.
        * 서로 다른 이미지에서 만든 쿼리와 키를 negative pair로 설정했다.
        * 이미지에 2개의 랜덤한 view(=augmentation)를 적용한다.
        * 쿼리와 키는 쿼리 인코더와 키 인코더에 의해 인코딩 된다.
        * algo 1이 전체 프로세스를 표현하고 있다.
        * positive pair: (랜덤 증강(이미지1), 랜덤 증강(이미지1))
        * negative pair: (랜덤 증강(이미지1), 랜덤 증강(이미지2))
        * negative pair는 (큐-1)개로 이미지2, 이미지3, ..., 이미지(큐-1)
        * 인코더로 ResNet 사용.
        * batch normalization이 방해하는 것을 발견하여 shuffling BN 사용.
<br><br>

### [결과 분석]
* Pre-training details
    * ImageNet, SGD, weight decay, batch=256, lr=0.03, epochs=200, lr step decay
* Linear classification
    * frozen feature를 사용해서 linear classification 진행했다.
    * 100 epochs 학습. lr=30, no weight decay.
    * Ablation: contrastive loss mechanisms
        * Fig 3
        * end to end 모델은 K가 1024 이상 넘으면 구동할 수 없다.
        * K는 key와 query가 다른 샘플인 negative pair의 개수.
        * 1024까지는 MoCo랑 비슷했는데 그 이상은 효율성 문제로 학습이 안된다.
        * memory bank 모델은 K가 커질 수는 있으나 MoCo 보다 좋지 않다.
    * Ablation: momentum
        * 모멘텀 값을 바꿔가며 실험했을 때 0.999가 제일 좋았다.
    * Comparison with previous results
        * Table 1
        * MoCo R50은 다른 24M 정도 크기의 모델 중에 가장 뛰어나다.
        * MoCo R50w4x은 모든 모델 중에 가장 뛰어나다.
* Transferring features
    * PASCAL VOC object detection
        * Table 2, Table 3, Table 4
    * COCO object detection and segmentation
        * Table 5
* Fine-tuning in ImageNet
    * 기존 76.5% -> MoCo(IG) 77.3%
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


