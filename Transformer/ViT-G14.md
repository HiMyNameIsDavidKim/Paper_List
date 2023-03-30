# Scaling Vision Transformers
* ZHAI, Xiaohua, et al. Scaling vision transformers. In: Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2022. p. 12104-12113.
* CVPR 2022
* 책갈피(2.0~)
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 스케일링은 높은 퍼포먼스를 위한 가장 중요한 요소다.
* ViT에 스케일링을 하는 방법은 연구가 없다.
* 스케일링 ViT를 위한 아키텍쳐와 트레이닝을 정의해보자.
<br><br>

### [기존 문제점]
* ViT에 스케일링 하는 방법을 모른다.
* ViT에 few-shot trasnfer learning 하는 방법을 모른다.
<br><br>

### [해결 아이디어]
* scaling up(계산량, 모델사이즈, 데이터셋 추가)
    * 더 많은 레이어의 모델로 더 큰 데이터셋 학습 시 실제로 representation quality가 증가한다.
    * 더 큰 모델만이 더 큰 데이터셋에서 이득을 얻을 수 있다.
    * 모델이 아무리 커져도 error가 saturation 된다. (irreducible entropy, performance ceiling)
* large-scale training을 위한 레시피 탐색
    * MLP 헤드에서만 weight decay
    * class token 삭제로 메모리 확보
    * 데이터셋 스케일링 업(JFT-3B)
    * 메모리 절약형 옵티마이저
    * LR 스케쥴러(웜업 비기닝 & 쿨다운 엔드)
    * 모델 스케일링 업(G/14)
<br><br>

### [추가로 볼 레퍼런스]
* Language models are few-shot learners
<br><br>

### [내 아이디어]
* 신형 모델을 어떻게 최적화하는지 봐야할듯.
* 일종의 머신으로 보고 최적화된 레시피를 찾는듯한 방향의 연구다.
<br><br>



## [`메모`]

### [배경 지식]
* Transfer performance
    * 사전학습된 모델을 특정 task에 fine tuning하여 얻은 성능에 대한 지표.
    * 사전학습이 실제 수행해야할 task와 유사할수록 높다.
* few shot transfer
    * 사전학습된 모델을 적은 양의 학습데이터를 이용해 새로운 태스크에 대해 fine-tuning.
* scaling law
    * 모델 크기와 로그(성능 향상)은 비례한다.
    * 모델 크기를 scaling up 하는 방법은 3가지.(레이어 추가, 벡터 차원 확장, 더 긴 시퀸스 IO)
* Pareto frontier
    * trade-off 관계가 있는 여러 목적 함수를 최적화 할 때 쓰는 용어.
    * 하나의 목적 함수를 최적화 하면 다른 목적 함수의 성능이 저하되는 지점.
<br><br>