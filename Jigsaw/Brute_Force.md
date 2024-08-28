# Image Reassembly Combining Deep Learning and Shortest Path Problem
* PAUMARD, Marie-Morgane; PICARD, David; TABIA, Hedi. Image reassembly combining deep learning and shortest path problem. In: Proceedings of the European conference on computer vision (ECCV). 2018. p. 153-167.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 분리되고 정렬되지 않은 이미지의 조각들을 재조립해보자.
* 이미지 조각 간의 상대적 위치를 예측하는 아키텍처를 제시한다.
* 재조립 전략으로 그래프 문제의 shortest path를 찾는다.
* MET 데이터셋으로부터 새로운 데이터셋을 발표한다.
<br><br>

### [기존 문제점]
* 기존 모델은 재조립 전략이 따로 존재하지 않고 모든 경우를 계산한다.
* 그래프 알고리즘을 사용하여 연산 횟수를 줄인다.
<br><br>

### [해결 아이디어]
* Relative position prediction
    * Formulation
        * Fig 2
        * center 조각과 non-center 조각을 넣어 feature를 추출한다.
        * center 조각과 바로 맞닿는 조각을 선별하는 문제를 먼저 푼다.
        * 이것은 이진 분류 문제이며, 총 8개의 맞닿는 조각을 선별한다.
        * 8개 클래스를 가진 분류 문제(position prediction)를 모델링한다.
        * 이 과정을 병합한 단일 모델도 제시했다. 
        * 단일 모델은 9개 클래스 분류(= 8개 위치 클래스 + others 클래스)를 푼다.
    * Architecture
        * Fig 3
        * FEN에 center 조각과 non-center 조각을 입력해 feature를 추출한다.
        * 이 당시에 있는 모델을 다 실험해봤는데 VGG-like 모델의 성능이 제일 좋았다.
        * (ResNet이 성능이 떨어진다는데... 내 생각엔 아닐 것 같다.)
        * 퍼즐 조각이 전체 이미지보다 1/9 사이즈로 작아서 VGG로 충분하다고 주장한다.
        * 글로벌 풀링이 있는 ResNet이 정보 보존력이 떨어진다고 주장한다.
        * (필자는 두 의견 모두 동의 못한다.)
        * 각 fragments의 feature 2개는 CL으로 합쳐진다.
        * CL은 저자들의 직전 논문에 나온 레이어로 Kronecker product를 사용한다.
        * 더 압축된 버전인 Hadamard product도 사용한 모델도 평가한다.
* Puzzle resolution
    * Formulation
        * case 1: center 조각 1개와 non-center 조각 8개의 정렬되지 않은 목록이 주어진 경우
        * case 2: 9개 조각이 주어지고 중심 조각을 알 수 없는 경우
        * case 3: 다른 이미지에서 추출한 others 조각이 같이 주어진 경우
* Graph formulation
    * 직소 퍼즐 문제를 해결하는 방법은 그래프에서 최단 경로를 찾는 것이다.
    * Dijkstra 혹은 그 변형을 사용했다.
    * 각 그래프는 소스 S로 시작해 싱크 T로 끝난다.
    * 그래프의 depth level은 각 조각을 뜻한다.
    * 깊이 i에서 모든 노드는 이전 조각을 모두 할당했을 때 조각 i를 둘 수 있는 위치다.
    * 각 edge는 classification score를 가중치로 받는다.
    * 그래프의 크기는 E, 엣지의 갯수는 N, 조각의 수 n, 위치의 수 p
    * Fig 4에서, 3 조각 예시(왼:central unknown, 오:central known)
    * center 조각을 아는 경우, (n=8, p=8), E=150k, N=100k
    * center 조각을 모르는 경우, (n=9, p=9), E=1.3M, N=1M
    * others가 섞인 경우, E=5.0e9, N=4.0e8
<br><br>

### [결과 분석]
* Experimental setup
    * ImageNet dataset, MET dataset
    * 398x398 -> (7+96+24) + (24+96+24) + (24+96+7) -> 96 crop
    * 평가를 분류 모델(ImageNet)과 조립 모델(MET)을 나눠서 한다.
* Classification
    * Table 2
    * 상대 위치 8개에 대한 classification 문제 (case 1)
    * 기존 모델 논문은 40% 이었는데, 이 저자들이 구현 시 57% 달성했다.
    * Concatenation, Kronecker product, Hadamard product 3가지 방식을 비교했다.
    * Kronecker product가 66.4%로 가장 좋았다.
    * Table 3
    * 이진 분류 모델은 92.5% 정확도를 얻었다.
    * 이진 분류 다음 이어서 상대 위치 8개 분류 모델을 평가한다.
    * Table 2의 결과에서 이야기 한 것처럼 66.4%를 얻었다.
    * 9개 분류 모델은 others 클래스를 추가한 통합 모델(이진 분류 + 8개 분류) 이다.
    * 64.2%를 얻어 통합 모델이 나쁘지 않은 결과를 얻었다.
* Reassembly
    * Table 4
    * MET에 fine-tuning한 뒤 MET로 평가한 것이다.
    * reconstruction accuracy는 퍼즐 레벨 정확도 이다.
    * position accuracy는 조각 레벨 정확도 이다.
    * Greedy 알고리즘과 Dijkstra 알고리즘을 비교, Dijkstra 더 좋은 결과를 보여준다.
    * (수치는 데이터 정합성이 의심되는 관계로 생략)
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


