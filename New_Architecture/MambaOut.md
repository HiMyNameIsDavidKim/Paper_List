# MambaOut: Do We Really Need Mamba for Vision?
* YU, Weihao; WANG, Xinchao. MambaOut: Do We Really Need Mamba for Vision?. arXiv preprint arXiv:2405.07992, 2024.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* Mamba는 어텐션 매커니즘의 2차함수 복잡성을 해결하는데 매우 뛰어나다.
* 근데 맘바는 긴 시퀀스와 autoregressive 특성의 task에서만 유리하다.
* (image classification task는 둘 다 아니므로 부적합하다.)
* (detection과 segmentation task는 긴 시퀀스만 적합하다.)
* 이 가설을 증명해보자.
* 맘바 블럭을 쌓긴 하지만 SSM 모듈은 빠진 MambaOut 모델을 만들어 증명해보자.
<br><br>

### [기존 문제점]
* 트랜스포머는 긴 시퀀스에 불리하다.
* 이를 해결하기 위해 나온 맘바는 비전에 맞게 개조해야 한다.
* Vision mamba, Vmamba 등이 나왔지만 여전히 퍼포먼스가 높지 않다.
* Do we really need Mamba for Vision? 이라는 의문이 나올 수 밖에 없다.
<br><br>

### [해결 아이디어]
* MambaOut
    * 맘바 블럭에서 SSM 모듈을 뺀 Gated CNN 구조를 만들었다.
    * 이 모델을 SSM을 포함한 비전 모델 Vim, Vmamba 등과 비교해보자.
* Conceptual discussion
    * What tasks is Mamba suitable for?
        * 맘바는 selective SSM을 사용한다.
        * SSM을 수식으로 설명하면 Equation (1), (2), (3)으로 표현할 수 있다.
        * Eq 2의 재귀적인 특징이 SSM(=RNN like)과 어텐션 구조를 구별하는 특징이다.
        * Long sequence
            * Fig 2
            * 여기서 hidden state인 h는 크기가 고정되어 있고 여기에 모든 이력 정보를 다 때려넣는다.
            * 이 고정된 크기는 메모리(컴퓨터 메모리 말고 기억력 메모리)에 필연적으로 손실이 온다는 것을 의미한다.
            * 하지만 고정된 크기는 연산의 복잡성을 줄여주는 것도 보장해준다.
            * 반면에 어텐션 구조는 모든 key와 value를 메모리에 저장한다.
            * 이 '커지는 메모리'는 이론적으로 손실이 없다.
            * 하지만 '커지는 메모리'는 연산의 복잡성이 점점 커지는 것을 의미한다.
            * 결론은 SSM의 구조는 본질적으로 손실이 발생한다.
            * 단기 기억 쪽에서는 이 손실 발생 아키텍처가 당연하게도 어텐션 구조의 무손실 기억보다 약할 수 밖에 없다.
            * 그래서 짧은 시퀀스에서는 맘바가 어텐션 구조를 이길 수 없다.
            * 하지만 긴 시퀀스 상황이 오면 어텐션 구조는 이차함수적 복잡성에 의해 흔들림이 생긴다.
            * 그래서 긴 시퀀스에서 맘바는 뚜렷한 장점을 보인다.
            * 메모리를 효율적으로 합치고 긴 시퀀스를 스무스하게 관리할 수 있다.
        * Autoregressive
            * Fig 3
            * SSM의 큰 문제는 h는 오직 이번과 직전 정보만 접근할 수 있다.
            * 이런 조건의 모델을 causal mode라고 명명하여 예를 들었다.
            * causal mode는 본인 직전까지의 토큰만 접근할 수 있다.
            * 예를들어 GPT's attention, Mamba's SSM이 있다.
            * 이 모드는 autoregressive한 generation task에 적합하다.
            * 반면에 fully-visible mode는 이후의 토큰까지 다 접근할 수 있다.
            * 예를들어 BERT's attention, ViT's attention이 있다.
            * 이 모드는 시퀀스 전체를 이해하는 understanding task에 적합하다.
            * 어텐션은 fully-visible mode가 기본이고 손쉽게 causal mode로 바꿀 수 있다.
            * 하지만 SSM은 본질적으로 이 모드를 넘나들 수 없다.
            * 따라서 맘바는 autoregressive한 task에 적합하다.
    * Do visual recognition tasks have very long sequences?
        * 이 부분은 ImageNet의 이미지는 224 밖에 안되기 때문에 긴 시퀀스일 필요가 없다는 말을 하고 있는데 동의하지 않기 때문에 넘어가겠다.
        * 이건 단순히 classification task에서 편의상 비교하기 쉽게 통일한 것이다.
        * 실제로 사용할 때는 긴 시퀀스에 많은 어려움을 겪고 있다.
        * 따라서 이런식으로 해석하는건 옳지 않다.
    * Do visual recognition tasks need causal token mixing mode?
        * Fig 3
        * 이미지 인식 분야는 시퀀스 전체를 이해하는 understanding task 이다.
        * 이후 토큰을 볼 수 없는 causal mode로 바꾸면 기능 저하가 생길 수 있다.
        * visual recognition task는 causal mode로 바꿀 필요가 없다.
    * Hypotheses regarding the necessity of Mamba for vision
        * 아무튼 저자들의 주장을 정리해보자.
        * 롱 시퀀스, autoregressive 2가지 특징이 없는 image classification에 SSM은 필요하지 않다.
        * 롱 시퀀스 특징은 있고 autoregressive 특징은 없는 detection이나 segmentation에 SSM은 사용해볼 가치가 있다.
    * Gated CNN and MambaOut
        * Fig 1
        * MambaOut 모델은 Gated CNN 블럭을 베이스로 한다.
        * Gated CNN과 Mamba의 가장 큰 차이는 SSM이 있고 없고 이다.
<br><br>

### [결과 분석]
* Experimental verification
    *  Image classification on ImageNet
        * Table 1
        * 비슷한 크기끼리 비교했을 때 SSM을 포함한 Mamba들 보다 SSM은 지운 MambaOut이 더 좋은 성능을 보인다.
        * classification에서는 SSM이 불필요하므로 제거하는게 좋다.
        * (나는 저자의 주장에 동의하지 않는다. 224 이미지로 실제 기술에 적용할 수 있는건 없다. 이건 단순히 평가일 뿐이다. 실제 기술에서는 최소 1080p 사이즈는 될 것이다. 이 task는 모델이 얼마나 이미지를 잘 해석 하는지를 보는 것이지 저 accuracy 0.X%가 중요한게 아니다.)
    * Object detection & instance segmentation on COCO
        * Table 2
        * MambaOut이 Mamba를 넘지 못했다.
        * 롱 시퀀스 특징이 있는 detection에서는 SSM이 필요하다.
    * Semantic segmentation on ADE20K
        * Table 3
        * MambaOut이 Mamba를 넘지 못했다.
        * 롱 시퀀스 특징이 있는 segmentation에서는 SSM이 필요하다.
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* SSM은 RNN과 유사하게 만든 state 행렬 예측 모델이다.
* Mamba는 SSM를 하드웨어에 맞춤형으로 연산하며 MLP를 사용한다.
<br><br>


