# Improved Baselines with Momentum Contrastive Learning
* CHEN, Xinlei, et al. Improved baselines with momentum contrastive learning. arXiv preprint arXiv:2003.04297, 2020.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* SimCLR을 MoCo 프레임워크 안에서 학습시켜보자.
* MoCo를 SimCLR의 MLP projection head, augmentation을 사용해 수정해보자.
* SimCLR의 관점에서는 거대한 학습 배치 문제를 개선해보자.
<br><br>

### [기존 문제점]
* MoCo의 작업 효율성 이 낮다.
* MoCo의 데이터 증강 기법이 너무 단순하다.
* SimCLR은 배치 사이즈가 너무 크다. (4k~8k 정도)
<br><br>

### [해결 아이디어]
* Contrastive learning
    * data에서 추출한 representation pair의 비슷한 정도를 학습하는 기법.
    * 이는 곧 dictionary look-up problem이라 생각할 수 있다.
    * 이를 수식으로 나타내면 contrastive loss 함수인 InfoNCE가 된다.
    * Eq 1
    * q: 쿼리의 representation, k: 키 샘플의 representation
    * +: positive pair 비슷한 것, -: negative pair 비슷하지 않은 것.
    * positive pair는 같은 이미지 2개에 다른 증강기법 적용.
    * negative pair는 다른 이미지 2개에 다른 증강기법 적용.
* MoCo
    * dictionary를 구축할 때 전체 배치를 사용하지 않고 queue 기법 적용.
    * 배치 사이즈와 dictionary(negative pair 전체) 사이즈를 분리했다.
* Improved designs
    * SimCLR는 기존 end-to-end 모델에서 3가지 차별점을 뒀다.
    * 첫번째는 4k or 8k의 거대한 배치로 negative 샘플을 늘린 것이다.
    * 두번째는 최종 FC 레이어를 MLP head로 바꿨다.
    * 세번째는 강력한 데이터 증강 기법을 사용했다.
    * MoCo는 이미 queue를 사용해서 충분히 큰 배치를 사용한다.
    * 따라서 두번째 세번째를 MoCo에 적용한다.
    * 이 두가지는 MoCo에서 사용한 메서드들과 'orthogonal'하다. 직역하자면 겹치는 것이 없고 전혀 다른 수직에 가까운 메서드 이다. 따라서 성능 향상을 기대해볼 수 있다.
<br><br>

### [결과 분석]
* 기본 세팅
    * 비지도학습에는 ImageNet training set을 사용했다.
    * 2가지 평가(ImageNet linear classification, VOC object detection)
    * backbone으로는 ResNet50을 사용했다.
* MLP head
    * 2-layer MLP head, 2048-d, ReLU 구성.
    * pre-training 에만 사용하고 fine-tuning에서는 버리는 헤드이다.
    * τ의 기본값 0.07에서도 linear probing 성능이 2% 향상 됐다.
    * τ가 커지면 커질수록 좋아진다.
    * Table 1을 보면 5.6%로 크게 향상됐다.
* Augmentation
    * 기존 MoCo에서 사용하던 방식에 blur와 color distortion을 추가했다.
    * Table 1을 보면 이것만 추가해도 성능이 2.8% 올라간다.
* Cosine annealing
    * 약간의 상승은 있었으나 큰 차이는 없다.
* Comparison with SimCLR
    * Table 2
    * 모든 기법을 적용한 MoCo v2의 200 에포크는 같은 배치 같은 에포크의 SimCLR과 비교했을 때 크게 성능을 앞선다.
    * 심지어 큰 배치의 SimCLR과 비교해도 더 좋다.
    * 최대 에포크로 늘려서 학습한 두 모델을 비교했을 때 SimCLR이 더 좋다.
* Computational cost
    * Table 3
    * SimCLR을 GPU에서 학습한다고 가정한다. (원래는 TPU)
    * 256 배치 사이즈 끼리 비교해도 MoCo v2가 더 메모리를 덜 쓴다.
    * back propagation에서 차이가 있기 때문이다.
    * SimCLR은 q,k 인코더를 둘 다 업데이트 하지만, MoCo는 q 인코더만 업데이트 하기 때문이다.
    * 4096 배치는 심지어 GPU에서 돌릴 수 없을 정도로 크다.
    * 굳이 큰 배치를 고수하지 않아도 충분히 좋은 모델을 만들 수 있다.
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


