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
    * NLP의 prompt 아이디어를 segmentation의 상황에 맞게 해석 하자.
    * 어떤 prompt가 주어져도 valid한 segmentation mask를 리턴하는 것.
    * Valid란 
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


