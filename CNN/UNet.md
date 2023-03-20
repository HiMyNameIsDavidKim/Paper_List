# U-Net: Convolutional Networks for Biomedical Image Segmentation
* RONNEBERGER, Olaf; FISCHER, Philipp; BROX, Thomas. U-net: Convolutional networks for biomedical image segmentation. In: Medical Image Computing and Computer-Assisted Intervention–MICCAI 2015: 18th International Conference, Munich, Germany, October 5-9, 2015, Proceedings, Part III 18. Springer International Publishing, 2015. p. 234-241.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 데이터 증강을 활용한 트레이닝 전략의 네트워크 아키텍쳐.
<br><br>

### [기존 문제점]
* 적은 수의 이미지로 학습이 잘 안된다.
* CNN 대부분은 위치정보를 내놓지는 않는다.
* 위치정보를 내놓는 모델은 단점이 있다.
    * 패치를 많이 쪼개기 때문에 느리다.
    * 패치 사이즈에 따라 큰 객체 or 작은 객체만 인식한다.
<br><br>

### [해결 아이디어]
* U shaped architecture
    * FCN에 skip connection을 추가한 구조.
    * 인코더는 입력 이미지의 low level feature 추출.
    * 디코더는 입력 이미지의 high level feature 복원 후 map 생성.
    * skip connection으로 low와 high 결합하여 정교한 map 생성.
    * context 손실 최소화, 위치정보 성능 개선.
<br><br>

### [추가로 볼 레퍼런스]
* FCN
<br><br>

### [내 아이디어]
* 위치정보에 유용하면 객체인식에 앙상블 시키면 좋아지나
<br><br>



## [`메모`]

### [배경 지식]
* FCN, Fully convolution network
    * semantic segmentation (픽셀 단위 분류) 전용.
    * 인코더 디코더 구조 사용. 공간 정보를 보존하며 출력 생성.
<br><br>