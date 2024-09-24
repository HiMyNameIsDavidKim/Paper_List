# SAM 2: Segment Anything in Images and Videos
* RAVI, Nikhila, et al. Sam 2: Segment anything in images and videos. arXiv preprint arXiv:2408.00714, 2024.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* promptable video segmentation (PVS) task를 풀기위한 SAM2에 대한 연구.
* video segmentation dataset을 위한 데이터 엔진을 구축했다.
<br><br>

### [기존 문제점]
* 이미지를 넘어 비디오도 segmentation할 모델이 필요하다.
    * 엔티티의 시공간적 범위가 늘어나야한다.
    * 비디오는 모션에 따라 엔티티가 변할 수 있다.
    * 이미지에 비해 엔티티의 품질이 낮다.
    * 프레임 수가 많기 때문에 효율적인 처리가 중요하다.
* PVS task를 이미지에서 비디오로 일반화 해야한다.
    * 입력에 따른 spatio-temporal mask (masklet)을 예측해야한다.
<br><br>

### [해결 아이디어]
* Task: promptable visual segmentation
    * 
* Model
    * object와 previous interaction을 저장하는 메모리가 있다.
    * 저장된 메모리를 활용해 이번 예측을 효과적으로 보정한다.
    * 
* Data engine
    * 기존 데이터셋들과 다르게 객체를 특정 카테고리에 제한하지 않는다.
    * vaild한 경계로 어떠한 객체도 segmenting 한다.
    * 이 엔진으로 만든 데이터셋 SA-V를 출시한다.
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


