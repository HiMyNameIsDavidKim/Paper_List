# Deep Residual Learning for Image Recognition
* HE, Kaiming, et al. Deep residual learning for image recognition. In: Proceedings of the IEEE conference on computer vision and pattern recognition. 2016. p. 770-778.
* CVPR 2016
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 레이어를 계속 늘려도 정확도가 계속 좋아지는 구조를 만들어 보자.
<br><br>

### [기존 문제점]
* 러닝 시 unreferenced function 사용.
* 레이어를 더 깊이 쌓을수 없다.(18 layer = 34 layer)
    * 문제1 : vanishing/exploding gradients.
    * normalized initialization이 해결.
    * 문제2 : 그래도 여전히 발생하는 degradation.
* 따라서, recognition에 한계가 있다.
<br><br>

### [해결 아이디어]
* 러닝 시 residual function을 사용하도록 레이어를 재구성했다.
    * with reference to the layer input.
    * 인풋 값을 그대로 더해주는 함수 구조.
* Depth가 더 깊어도 어큐러시가 좋은 모델이 된다.(18 layer < 34 layer)
* 최적화 하기 더 쉽다. 따라서 어큐러시가 올라간다.
* 아이디어 도출 과정
    * deeper 모델에 레이어를 추가로 구축.
    * identity mapping 레이어.
    * residual learning 개념.
        * 원본 데이터를 shortcut으로 연결.
        * 기본 매핑(desired underlying)이 아니라 레지듀얼 매핑(residual) 진행.
        * F(x)+x 형태. 2개 이상의 레이어를 스킵하고 바로 연결.
        * 원본 데이터를 그대로 전달한다는 뜻에서 identity mapping 용어 사용.(cf. identity 행렬)
        * 추가로 선언한게 없으므로 복잡도의 증가가 없다.
    * bottleneck 개념.
        * 빌딩블록을 다른 형태로 변경. 더 경제적인 이유 때문.
        * 2개의 레이어(3x3, 3x3) -> 3개의 레이어(1x1, 3x3, 1x1)
        * 1x1 : 차원 축소.
        * 3x3 : 합성곱 연산.
        * 1x1 : 차원 복원.
        * 차원(=채널)이 줄어들기 때문에 연산이 적고, 프로젝션 없이 그대로 더하면 된다.
        * 저차원에서 연산을 하되, 기존 차원의 중요한 정보는 residual connection으로 보존한다.
    * Identity vs Projection
        * Identity : 진짜 x 그대로 아무 손도 안댄 날 것. parameter free.
        * Projection : 리니어 프로젝션인 스퀘어 매트릭스와 곱한 결과. (차원 맞추기)
        * 왜 차원증가에서만 프로젝션 숏컷을 썼는지에 대한 실험적 데이터.
        * 3개 케이스 비교.
        * (A: 차원증가를 위해 패딩 사용, 모든 숏컷은 identity 그대로.)
        * (B: 차원증가를 위해 프로젝션 숏컷 사용, 나머지 숏컷은 identity 그대로.)
        * (C: 모든 숏컷은 프로젝션 숏컷 사용.)
        * 당연히 B가 A보다 잘나온다. 패딩은 Residual Learning이 안된 것이다.
        * 근데 C가 B보다 잘나온다. 너무 미세하게 잘나온다.
        * 메모리 절약, 복잡도 감소를 위해 B 채택.
<br><br>

### [추가로 볼 레퍼런스]
* VGGNet
* Batch Normalization
* Adam
<br><br>