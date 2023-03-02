# Deep Residual Learning for Image Recognition
* Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun, Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2016, pp. 770-778
* 책갈피(3.3부터 읽기)
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 레이어를 계속 늘려도 정확도가 계속 좋아지는 구조를 만들어 보자.
<br><br>

### [기존 문제점]
* 러닝 시 unrefernced function 사용.
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
    * identity mapping 레이어, shallower 모델에게 배우는 레이어.
    * deep residual learning 구조 발명.
    * 기본 매핑(desired underlying)이 아니라 레지듀얼 매핑(residual) 진행.
    * F(x)+x 형태. 2개 이상의 레이어를 스킵하고 바로 연결.
    * 원본 데이터를 그대로 전달한다는 뜻에서 identity mapping 용어 사용.(cf. identity 행렬)
    * 추가로 선언한게 없으므로 복잡도의 증가가 없다.
<br><br>

### [내가 사용할 부분]
* X
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>