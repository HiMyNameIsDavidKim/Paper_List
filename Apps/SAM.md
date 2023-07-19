# 📓Paper List

## [`논문 요약 양식`]

### [저자의 의도]
* NLP에는 GPT 모델 같은 혁신적인 모델이 있다.
* 그러나 CV에는 그런 모델이 없다.
* CV의 Foundation Model을 디자인 해보자. (zero-shot transfer)
* SAM을 출시하고, CV의 foundation model 연구 촉진을 하자.
* 이 모델을 학습시키기 위한 초거대 segmentation dataset을 만들어보자.
<br><br>

### [기존 문제점]
* NLP의 거대한 capability는 prompt engineering으로 구현되는 편이다.
* 하지만 CV에는 다양한 문제점이 있고, 특히 풍부한 train dataset이 부족하다.
<br><br>

### [해결 아이디어]
* promptable 모델을 설계하여 광범위한 dataset으로 pre-training 하자.
    * 3가지 메인 구성요소 정의(task, model, data)
    * 충분히 보편적인 task, flexible prompting을 지원하는 model, 거대한 원천 data
* Task
    * Task
        * NLP의 prompt 아이디어를 segmentation의 상황에 맞게 해석 하자.
        * 어떤 prompt가 주어져도 valid한 segmentation mask를 리턴하는 것.
        * Valid란 prompt가 여러가지로 해석될 수 있더라도 output 중 1개는 반드시 타당해야 한다.
        * (LLM이 다중의미의 prompt에도 일관성 있는 대답을 하는 것처럼)
        * 이런 task가 자연스러운 pre-training 알고리즘으로 이끌고, downstream에 대하여 보편적으로 작용하게 한다.
    * Pre-training
        * 각 샘플에 대하여 prompt의 시퀸스가 주어진다. (points, boxes, masks)
        * 모델의 마스크 예측과 ground truths와 비교한다.
        * 이 방법을 interactive segmentation에 적용한다.
        * (interactive segmentation: 사용자 클릭을 사용하여 CNN을 트레이닝 시킴)
    * Discussion
        * Prompting and composition은 모델이 확장하는 방법에 있어 매우 파워풀한 툴이다.
        * 복합적인 시스템 설계가 더 다양한 downstream 작업을 가능하게 할 것으로 기대할 수 있다.
* Model
    * Overview
        * 아키텍쳐는 크게 3가지로 나눌 수 있다.
        * image encoder + flexible prompt encoder + fast mask decoder
    * Image encoder
        * MAE로 프리 트레이닝된 ViT-H/16 이다.
        * MAE는 Masked AutoEncoder는 높은 scalability와 강력한 pre-training이 가능하게 해준다.
        * 인풋 이미지 : (1024 * 1024) size * (256 * 3) channels
        * 패치 사이즈 = 16이며, 채널에 (1x1 Conv) (3x3 Conv) (Norm) 진행.
        * 아웃풋 임베딩 : (64 * 64) patches * 256 dimensions
    * Prompt encoder
        * 
<br><br>

### [추가로 볼 레퍼런스]
* interactive segmentation
* CLIP
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* 
<br><br>


