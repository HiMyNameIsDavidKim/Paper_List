# Jigsaw puzzle solving using local feature co-occurrences in deep neural networks
* PAUMARD, Marie-Morgane; PICARD, David; TABIA, Hedi. Jigsaw puzzle solving using local feature co-occurrences in deep neural networks. In: 2018 25th IEEE international conference on image processing (ICIP). IEEE, 2018. p. 1018-1022.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 고고학자들은 자동화된 객체 재구성 메서드를 절실히 필요로 한다.
* 조각을 조립하는 것은 비전 알고리즘으로 퍼즐을 푸는 문제와 유사하다.
* 상대적인 위치를 classify 하는 것에 집중한다.
* 상대적인 위치 예측을 기반으로 한 greedy 알고리즘을 제시한다.
<br><br>

### [기존 문제점]
* 고고학에서 예술 작품을 재구성하는 것은 시간이 매우 많이 소모되는 task 이다.
* Fig 1
* 직소 퍼즐 모델을 학습시켜 이 문제를 해결해보자.
* 직소 퍼즐 모델을 처음으로 정립한 저자는 직소 퍼즐 task 자체보다 pretext task로써 사용한다.
* 기존 모델을 확장시켜보자. (combination layer, new dataset)
<br><br>

### [해결 아이디어]
* Feature Extraction Network architecture
    * Table 1
    * FEN으로 VGG를 사용하고 96x96 사이즈의 인풋을 받는다.
    * FEN은 공간적 레이아웃을 유지하도록 설계되어 있다.
* Combination Layer
    * 이 논문의 핵심 아이디어 이다.
    * 일단 목적은 두 조각의 상대적 위치를 알아내는 것인 점을 기억하자.
    * 동일한 FEN을 사용해서 두 조각의 feature를 추출했다.
    * 이 두 feature를 합쳐주기 위해 combination layer를 사용한다.
    * 기존에는 feature를 합치기 위해 더하는 방법을 사용했다.
    * 저자들은 바로 더하는게 아니라 약간의 응용으로 함수를 한번 거친 뒤 더했다.
    * linear combination 방법을 수식으로 나타내면 다음과 같다.
    * linear combination이라 해석했다. (FC layer 함수 연산의 합)
    * linear combination이 co-occurrences를 추출하기에 적절하지 않다고 주장한다.
    * 그래서 Kronecker product 연산을 사용해 더하고자 했다.
    * Kronecker product 방법을 수식으로 나타내면 다음과 같다.
* Puzzle solving
    * center 조각과 non-center 조각을 함께 주고 8개 위치 중 1개로 예측한다.
    * 결과는 확률으로 구성된 8x8 크기 행렬으로 나온다.
    * 이 값을 이용해 모든 permutation set에 대하여 확률 값의 합을 구한다.
    * 그 중에서 확률 값이 가장 큰 permutation을 선택한다.
    * 경우의 수 permutation이 너무 크기 때문에 greedy 알고리즘을 사용해 속도를 개선한다.
<br><br>

### [결과 분석]
* MET dataset and experimental setup
    * ImageNet 대신에 사용했다.
    * 더 좋은 카메라로 찍었고 색상 분포가 넓지 않은 특징이 있다.
    * Metropolitan Museum of Art 이미지 14k개
    * 10k개 = train, 4k개 = eval
    * 조각 1개는 96x96 사이즈이며 각 조각은 48 사이즈의 갭을 가지고 있다.
    * 모든 조각은 모든 방향으로 랜덤하게 7픽셀 정도 이동한다.
    * 3가지 스텝에 대하여 평가한다.
        * 이미지넷 학습 -> 이미지넷 평가
        * 이미지넷 학습 -> MET fine-tuning -> MET 평가
        * MET 학습 -> MET 평가
* Results
    * Fig 5
        * 첫번째 스텝 ImageNet에 대한 평가를 먼저 보자.
        * 기존 논문을 구현해본 결과 40%가 아닌 57% 정확도가 나왔다.
        * 저자들의 combination layer를 추가한 방법 두가지 모두 기존보다 우수하다.
        * (논문에서는 concatenation이라고 적혀 있는데 너무 옛날 논문이라 그런지 그냥 번역기를 돌려서 적은건지 우리가 알고 있는 concatnate와는 전혀 다른 뜻인다.)
        * Kronecker product가 combination보다 근소하게 더 좋다.
    * Table 2
        * 두번째 세번째 스텝에 대한 평가를 보자.
        * 이미지넷 사전학습을 하는 경우가 MET만 학습한 경우보다 더 좋은 결과가 나온다.
        * 이미지넷 학습 후 MET를 fine-tuning한 경우 kronecker 방법이 크게 앞선다.
    * Fig 4
        * 실패 case를 분석했다.
        * 비슷한 퍼즐 조각의 경우 바꾸는 경우가 있었다.
        * 데이터를 살펴보니 이런 경우가 많았다.
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


