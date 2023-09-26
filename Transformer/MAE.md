# Masked Autoencoders Are Scalable Vision Learners
* HE, Kaiming, et al. Masked autoencoders are scalable vision learners. In: Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 2022. p. 16000-16009.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* MAE는 CV분야의 scalable한 slef-supervised 모델이다.
* 큰 모델을 효율적이고 효과적으로 학습해보자.
* generalize가 잘 되는 높은 capacity를 가진 모델에 대하여 접근해보자.
<br><br>

### [기존 문제점]
* NLP의 BERT 모델에서 masked autoencoder 메서드를 잘 사용하고 있으나, CV에는 비교적 성능이 떨어진다.
* NLP와 CV의 어떤점이 MAE에 차이점을 야기하는지 생각해보자.
    * CNN과 BERT의 architecture 차이(ViT 등장으로 어느정도 해결)
    * Information density
    * Autoencoder의 Decoder 부분
<br><br>

### [해결 아이디어]
* Masking a high portion
    * 언어는 인간이 만든 시그널이고, 이미지는 자연이 만든 시그널이다.
    * 언어는 이미지에 비해서 높은 밀도의 정보를 가지고 있다.
    * NLP 모델은 오직 적은 수의 단어만 예측하는데, 그렇다고 해서 CV에서 적은 양의 mask를 해선 안된다. 정보 밀도의 수준이 다르기 때문이다.
    * 이 전략은 정보 과잉을 해결해주고, 사진 전체를 보는 능력을 요구한다.
* Masking ramdom patches
    * CV에서는 디코더가 pixel 재구성 작업을 하는데 이것은 recognition에 비해 low semantic level의 작업이다.
    * NLP에서는 디코더가 missing word 재구성 작업을 하는데 이것은 high semantic level의 작업이다.
    * 따라서, pixel이 아닌 patches를 재구성 하게 만들고, token(=포지션)을 가려주어야 비슷한 수준의 작업이 된다.
* Approach
    * Masking
        * ViT처럼 MAE도 이미지를 패치로 쪼갠다.
        * 높은 마스킹 비율로 랜덤 샘플링하면 정보 과잉을 줄일 수 있다.
        * 작업이 주변 패치에 의해 너무 손쉽게 풀리는 경우를 방지한다.
    * MAE encoder
        * MAE의 인코더는 ViT와 동일하지만 오직 마스킹되지 않은 패치만 인풋된다.
        * 인풋과 아웃풋 모두 전체 사진에서 작은 포션(약 25%)의 패치 세트로 구성된다.
        * 이로써 계산량과 메모리의 일부만 사용하면서 매우 큰 크기의 인코더를 학습시킬 수 있다.
    * MAE decoder
        * 
<br><br>

### [추가로 볼 레퍼런스]
* Accurate, large minibatch SGD: Training ImageNet in 1 hour
    * lr, warm up 관련.
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* capability
    * 모델이 얼마나 복잡한 함수를 학습하거나 모델이 어떤 유형의 작업을 수행할 수 있는지에 대한 능력
    * 더 복잡한 패턴이나 관계를 학습
* capacity
    * 모델 내부의 매개변수(parameter)의 개수나 모델의 구조의 용량
    * 더 복잡한 함수를 표현
    *  generalize가 잘 된다.
* linear probing
    * 사전 학습된 모델의 출력 특징은 고정하고 출력된 특징을 기반으로 학습.
    * MLP head만 weight를 새로 학습하는 것.
    * 사전 학습된 모델 전체를 다시 학습하는 fine-tuning과는 다른 방법이다.
    * 더 빨리 학습시킬 수 있으나, 성능이 떨어질 수 있다.
<br><br>


