# BinsFormer: Revisiting Adaptive Bins for Monocular Depth Estimation
* LI, Zhenyu, et al. Binsformer: Revisiting adaptive bins for monocular depth estimation. arXiv preprint arXiv:2204.00987, 2022.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 
<br><br>

### [기존 문제점]
* 
<br><br>

### [해결 아이디어]
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
* classification-regression task
    * Reg: 연속값 예측. 각 픽셀의 뎁스 예측. Reg loss 계산.
    * Class: 이산값 예측. probabilistic representation 예측. Class loss 계산.
    * Class-Reg: probabilistic representation 예측 후 bins center와 리니어 컴비네이션을 통해 연속값 예측.
    * probabilistic representation: 객체가 클래스에 속할 확률 예측하는 것.
    * bins: 연속 범위를 구간을 나눈 것. (ex. 0~6 -> [0,2], [2,4], [4,6])
    * bins center: 각 bins의 중심값. (ex. 1, 3, 5)
* AdaBins
    * 인풋 이미지를 분석하여 bins마다 범위가 같은게 아니라 최적화된 범위의 bins를 구현.
<br><br>


