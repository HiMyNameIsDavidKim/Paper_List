# Scaling Vision Transformers
* ZHAI, Xiaohua, et al. Scaling vision transformers. In: Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2022. p. 12104-12113.
* CVPR 2022
* 책갈피()
<br><br>

## [`논문 요약`]

### [저자의 의도]
* NLP에서 트랜스포머 성능 향상에 scaling이 중요하다.
* 비전 트랜스포머에서도 scaling을 해보자.
<br><br>

### [기존 문제점]
* ViT에서는 scaling과 few-shot transfer를 시도한 적이 없다.
<br><br>

### [해결 아이디어]
* scaling
    * 
* Few-Shot Transfer 반복 실험
    * 
* 최종 MLP 레이어에서 강력한 L2 사용
    * 
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
<br><br>


