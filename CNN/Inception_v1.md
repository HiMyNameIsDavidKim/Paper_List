# Going deeper with convolutions
* SZEGEDY, Christian, et al. Going deeper with convolutions. In: Proceedings of the IEEE conference on computer vision and pattern recognition. 2015. p. 1-9.
* CVPR 2015
<br><br>

## [`논문 요약`]

### [저자의 의도]
* gradient vanishing이 없는 더 깊은 네트워크.
* 효율적으로 다양한 크기의 필터를 사용해보자.
<br><br>

### [기존 문제점]
* 깊어질수록 gradient vanishing 문제 발생.
* 입력되는 이미지의 사이즈가 일정하지 않은 경우 정확도 떨어진다.
* 여러 종류의 필터를 위해서는 여러 종류의 커널을 사용해야 한다.
<br><br>

### [해결 아이디어]
* 인셉션 모듈
    * 다양한 크기의 필터를 함께 사용하는 효율적인 블럭.
    * 1x1, 3x3, 5x5, 맥스풀링 4개를 통과시키고 concatenate.
    * 1x1 : 채널간 선형 결합으로 채널 수가 줄어든 원본 정보 전달.
    * 3x3, 5x5 : 작은 크기, 큰 크기의 특징 추출.
    * 맥스 풀링 : 컨볼루션 레이어에 비해 위치 이동에 영향을 받지 않고 정보 전달.
    * 1x1을 사용해 차원축소 -> 계산량 감소, 복잡도 감소, 병목 현상 예방.
* auxiliary classifier
    * 중간 지점에 해당하는 gradient와 적은 수의 파라미터로 역전파에서 gradient vanishing 완화. 
    * 네트워크가 깊어짐에 따라 gradient vanishing 발생.
    * 중간중간에 (AvgPool - 1x1 - FC - FC - Softmax)로 구성된 classifier 2번 진행.
    * 끝까지 학습이 안된 상태이므로 소실이 덜 된 그라디언트 전달 가능.
    * 가중치 부여로 얼마나 전달할지 조절 가능. 설정값 = (0.3, 0.4, 0.5)
* knobs and levers
    * 인셉션 모듈 내에서 필터수와 차원수를 튜닝하는 도구.
    * 실험적으로 더 뛰어난 모델 아키텍쳐를 탐색한다.
    * 노브 : 필터의 개수와 차원 수를 조절.
    * 레버 : 입력 데이터의 채널 수와 차원 수를 조절.
<br><br>

### [추가로 볼 레퍼런스]
* AlexNet
* R-CNN
<br><br>

### [내 아이디어]
* 3x3 맥스 풀링 층이 `위치 이동에 영향을 받지 않는다`면 아주 중요한 정보 아닌가
* 역전파 방지를 위해 중간중간에 총 3번 softmax 한게 신기함.
<br><br>



## [`메모`]

### [배경 지식]
* Hebbian principle
    * 뉴런이 학습할 때 사용되는 학습원리 중 하나.
    * 함께 활성화된 뉴런들은 서로 연결되는 경향이 있다.
    * 입력과 출력 뉴런이 동시에 활성화되면 이들 간의 연결 강도가 강화된다.
    * 같은 입력이 주어졌을 때 더 높은 출력값을 내놓을 수 있도록 학습된다.
<br><br>