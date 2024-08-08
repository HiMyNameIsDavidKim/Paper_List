# Image Reassembly Combining Deep Learning and Shortest Path Problem
* PAUMARD, Marie-Morgane; PICARD, David; TABIA, Hedi. Image reassembly combining deep learning and shortest path problem. In: Proceedings of the European conference on computer vision (ECCV). 2018. p. 153-167.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 분리되고 손상된 이미지의 조각들을 재조립해보자.
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
        * 
<br><br>

### [결과 분석]
* 
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


