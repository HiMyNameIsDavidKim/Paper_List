# iBOT: Image BERT Pre-Training with Online Tokenizer
* ZHOU, Jinghao, et al. ibot: Image bert pre-training with online tokenizer. arXiv preprint arXiv:2111.07832, 2021.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* MIM 연구를 통해 visual tokenizer의 장점과 문제점을 연구해보자.
* online tokenizer를 사용한 self-supervised 프레임워크 iBOT를 제시한다.
* 온라인의 의미는 사전 학습된 고정값이 아니라 모델과 함께 학습되는 것을 뜻한다.
* 마스크 패치 토큰에 self-distillation을 해서 teacher 네트워크로 사용해보자.
* (online tokenizer = teacher 네트워크)
<br><br>

### [기존 문제점]
* MLM은 NLP에서 사실상의 표준이 되었지만 CV의 MIM은 아직 부족하다.
* MLM의 가장 중요한 요소는 lingual tokenizer인데, visual tokenizer는 개선이 필요하다.
* 기존 연구에서는 pixel value를 예측했으나, 추상화된 의미를 학습하지 못하고 픽셀 디테일을 복원해야 하므로 비효율적이다.
* BEiT와 같은 연구에서는 VAE를 visual tokenizer로 사용했지만, 이건 오프라인 데이터셋에서 학습되어 적응력에 한계가 있다.
<br><br>

### [해결 아이디어]
* iBOT overview
    * Fig 3
    * image BERT pre-training with Online Tokenizer
    * MIM을 knowledge distillation(KD)로 구성한다.
    * 온라인 토크나이저를 twin teacher로 사용해 self-distillation 한다.
    * student 네트워크는 마스킹된 이미지를 받아 토크나이저의 아웃풋을 예측한다.
* MIM as Knowledge distillation
    * 먼저 MIM 프로세스에 대한 설명이다.
    * 이미지 토큰 시퀀스 x를 받으면, 0과 1으로 구성된 랜덤 마스크 m을 샘플링한다.
    * 이때 m의 개수는 토큰 시퀀스와 같은 길이인 N 이다.
    * i번째 패치 토큰 x_i는 랜덤 마스크 m_i가 1일 경우 마스크 토큰 e로 대체된다.
    * 이 결과가 손상시킨 이미지(corrupted image) x햇 이다.
    * MIM은 x햇에서 마스킹된 토큰 x틸드를 받아 원래 이미지 토큰 x을 복원(reconstruction) 한다.
    * 이를 수학적으로 나타내면 Eq 1이 된다. (=BEiT)
    * 여기서 기억해야 할 점은 이게 '이미지 토큰'에서 일어나는 일이다.
* Self-distillation
    * DINO가 유사한 방법의 학습을 먼저 제안했는데 MoCo의 방법을 사용했다.
    * MoCo는 저자들이 사용하려는 VAE와는 다른데, 과거 이터레이션을 마치 teacher처럼 사용한다.
    * 이를 수학적으로 나타내면 Eq 2가 된다. (DINO)
    * 여기서 기억해야 할 점은 이게 '클래스 토큰'에서 일어나는 일이다.
    * (링크 참고)
* iBOT
    * 저자들은 Eq 1과 Eq 2에서 모티브를 따서 iBOT을 설계했다.
    * BEiT는 학습된 VAE를 사용했는데, 이를 온라인으로(모델과 같이) 학습을 시킨다면 더 자연스러워 질 것이라 생각했다.
    * 다시 말하면 '온라인 비주얼 토크나이저'를 만들고자 했다.
    * self-distillation 아이디어의 경우 토큰 생성에 사용하고자 했다.
    * Framework
        * Fig 3
        * (self-distillation 부분 1)
        * 서로 다른 augmentation을 시킨 u와 v가 있다.
        * 여기에 blockwise masking으로 일부를 가려 u햇, v햇 이라 한다.
        * u햇을 student에 입력한 결과는 P_θ(u햇) 이다.
        * u햇을 teacher에 입력한 결과는 P_θ'(u햇) 이다.
        * training objective를 수식으로 나타내면 Eq 3이 된다.
        * u햇의 Eq 3과 v햇의 Eq 3을 평균을 내어 계산한다.
        * (MIM 부분 1)
        * 그림을 잘 보면 클래스 토큰을 처리하는 헤드 h_CLS, 이미지 토큰을 처리하는 헤드 h_patch가 별개이다.
        * 여기서 teacher의 이미지 토큰을 처리하는 h_patch_t는 비주얼 토크나이저 이다.
        * 이 비주얼 토크나이저가 masked 패치 토큰에 대한 온라인 토큰을 만든다.
        * 비주얼 토크나이저는 사전 학습을 위해 추가 단계가 필요하지 않다.
        * 또한 이 토크나이저는 특정 데이터셋으로 학습한게 아니라 현재 데이터셋을 기준으로 도메인 knowledge를 추출한다.
        * (self-distillation 부분 2)
        * Eq 3의 클래스 토큰 부분을 보면 L_cls가 엑스 모양으로 표현되어 있다.
        * 온라인 토크나이저가 유의미하게 만들기 위해 이렇게 했다.
        * 크로스뷰로 클래스 토큰 간에 self-distillation 하는 것이다.
        * 이 방법을 사용하면 시각적 의미가 부트스트래핑 된다... (필자도 이해 못함)
        * 자세한 내용은 레퍼런스 논문을 보도록 하자.
        * (self-distillation 부분 3)
        * teacher는 back propagation 하지 않고 EMA를 통해서 업데이트 한다.
        * (추가 사항)
        * NLP의 '단어'와는 다르게 CV의 '이미지 패치'는 상대적으로 모호하다.
        * 따라서 원핫 인코딩을 통한 토크나이제이션은 최적화되지 않을 수 있다.
        * 원핫 인코딩하지 않은 (softmax 직전의 raw 확률 분포)를 사용했다.
<br><br>

### [결과 분석]
* Implementation
    * ViT를 백본 인코더 f로 사용, ImageNet-1K, image size 224, AdamW, batch size 1024, (S 800/B 400/L 250) epochs, warm up, lr 5e-04, random MIM random 비율 0.1~0.5, L_cls와 L_MIM은 1대1로 더함.
* Experiment
    * Classification
        * 
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]