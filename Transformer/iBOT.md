# iBOT: Image BERT Pre-Training with Online Tokenizer
* ZHOU, Jinghao, et al. ibot: Image bert pre-training with online tokenizer. arXiv preprint arXiv:2111.07832, 2021.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* MIM 연구를 통해 visual tokenizer의 장점과 문제점을 연구해보자.
* online tokenizer를 사용한 self-supervised 프레임워크 iBOT를 제시한다.
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
    * VAE는 이미지 패치를 K개의 카테고리로 어사인해 분류한 토크나이저 이다.
    * 저자들은 이 방법이 knowledge distillation과 유사하다고 생각했다.
* Self-distillation
    * DINO과 유사한 방법의 학습을 먼저 제안했는데 MoCo의 방법을 사용했다.
    * MoCo는 저자들이 사용하려는 VAE와는 다른데, 과거 이터레이션을 마치 teacher처럼 사용한다.
    * 
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