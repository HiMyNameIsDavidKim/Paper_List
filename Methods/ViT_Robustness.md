# 📓Paper List
* Understanding and Improving Robustness of Vision Transformers through Patch-based Negative Augmentation
* QIN, Yao, et al. Understanding and improving robustness of vision transformers throug
* NeurIPS 2022.
<br><br>

## [`논문 요약 양식`]

### [저자의 의도]
* ViT는 여전히 non-robust 하다.
* ViT의 robustness와 퍼포먼스를 향상 시킬 방법은?
* patch-based negative augmentation이 ViT의 robustness을 향상시킨다.
<br><br>

### [기존 문제점]
* ViT는 CNN보다는 robustness가 좋지만 여전히 좋지 않다.
<br><br>

### [해결 아이디어]
* ViT는 놀라울 정도로 patch-based transformation에 둔감하다.
* 이 둔감성이 non-robust를 유발하는 것이 아닐까 확인해보자.
* 사람이 인지할 수 없는 feature에 대한 ViT의 의존성
    * ViT는 patch-based transformation으로 semantic이 파괴된 이미지도 높은 신뢰도로 맞춘다.
    * 사람은 전혀 알아볼 수 없을 정도로 파괴해도 마찬가지.
    * ViT가 어떻게 이미지를 인지하는지 이해할 수 없다.
* no-indicative feature와 robustness의 관계
    * 실험적 결과, patch-based transformation를 직접적으로 사용해서는 안된다.
    * accuracy가 기본 테스트셋도 떨어지고, out-of-distribution 데이터셋도 떨어짐.
* Negative augmentation
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
* robustness
    * 모델이 입력의 다양한 변화와 노이즈에 얼마나 견고한지에 대한 지표.
    * NLP에서 띄어쓰기, 오타 등이 있어도 정확하게 번역되는 것.
    * ViT는 pre-train dataset이 클수록 robustness함.
    * ViT는 CNN계열과 비교하여 robustness가 좋은 종류가 다름.
* 고도로 작은 패치를 사용하는 ViT는 픽셀 주변을 탐색하는 CNN과 유사한 특징이 있다.
* semantic-preserving augmentation
    * 증강을 위해 변형된 데이터도 원래 데이터와 동일한 의미를 가져야만 한다.
* ImageNet out-of-distribution dataset
    * ImageNet-A
        * real-world 이미지. ResNet이 구별하지 못한 이미지들만 모아놓은 것.
        * 네트워크의 취약성을 집중적으로 확인.
    * ImageNet-C
        * 회전, 블러, 노이즈, 해상도 드랍, 그림자 등.
        * 잘 알려진 robustness 평가.
    * ImageNet-R
        * 만화, 그림, 예술, 타투, 애니메이션 등.
        * 다른 분포의 데이터에서도 잘 동작하는지 일반화 능력을 평가.
<br><br>


