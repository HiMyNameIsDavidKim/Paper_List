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
* 
<br><br>

### [해결 아이디어]
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


