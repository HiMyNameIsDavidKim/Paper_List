# An Empirical Study of Training Self-Supervised Vision Transformers
* CHEN, Xinlei; XIE, Saining; HE, Kaiming. An empirical study of training self-supervised vision transformers. In: Proceedings of the IEEE/CVF international conference on computer vision. 2021. p. 9640-9649.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 새로운 방법을 제시하는 것은 아니다.
* ViT의 self-supervised learning(SSL)에 대한 연구다.
* CNN의 레시피는 상당히 성숙하고 robust한 것에 비하여 ViT는 미숙하다.
* 특히 SSL에서는 더욱 부족한 경향이 있다.
* ViT의 부분적인 실패들을 분석해서 안정적이게 만들자.
<br><br>

### [기존 문제점]
* 프리 트레이닝 부분에서 NLP와 CV가 격차가 너무 크다.
    * ViT의 self-supervised framework를 만들어야 한다.
* CNN과 ViT는 완전히 다른 모델이고 기존 CNN 프레임워크는 통하지 않는다.
    * 흥미로운 점은, 불안정한 ViT의 학습이 치명적인 실패가 아니라 약간의 퍼포먼스 저하만 생긴다는 점이다.
    * 따라서 ViT가 불안정한지 안정한지 판단하기 매우 어렵다.
    * 이런 문제점을 입증하기 위해 저자들은 간단한 트릭을 사용했다.
    * 저자들은 경험적으로 patch projection layer를 고정하면 된다고 말한다.
<br><br>

### [해결 아이디어]
* MoCo v3
    * MoCo v1과 v2에 비해 단순성, 정확성, 확장성 간의 밸런스를 높였다.
    * Algo 1
    * 일반적인 접근법과 동일하게 각 이미지에 랜덤 증강을 적용해 2개의 crop을 얻는다.
    * 이 2개의 crop은 각각 쿼리 인코더, 키 인코더에 의해 인코딩 된다.
    * 인코더의 결과물로 벡터 q와 k를 얻는다.
    * 각각 쿼리와 키의 역할을 하고 학습의 목표는 쿼리로 키를 검색하는 것이다.
    * 이는 contrastive loss function을 최소화하는 것으로 공식화할 수 있다.
    * 우리는 이를 InfoNCE 라 하며 Eq 1으로 나타낸다.
    * 여기서 k+는 쿼리와 같은 이미지를 키 인코더로 인코딩한 것이다.
    * 그리고 k-는 쿼리와 다른 이미지를 키 인코더로 인코딩한 것이다.
    * 이번 버전부터 MoCo는 dictionary queue 방식을 포기한다.
    * 대신 거대한 사이즈의 배치 (ex. 4096)를 채택했다.
    * 인코더들은 ResNet 혹은 ViT를 backbone으로 사용한다.
    * 앞선 버전의 MoCo들 처럼 키 인코더의 파라미터는 큐 인코더의 moving average를 통해서 천천히 업데이트 시킨다.
* Stability of self-supervised ViT training
    * 개요
        * 프레임워크에서 간단하게 backbone을 R50에서 ViT로 바꾸고자 했다.
        * 그런데 학습의 불안정성에 직면했다.
        * 불안정성 문제는 정확도로 관측하기 어려웠다.
        * 왜냐하면 불안정성이 있다고 판단할 만큼 낮은 퍼포먼스가 아니기 때문이다.
        * 저자들은 kNN 커브를 그려보고 나서야 이를 증명할 수 있었다. (Appendix)
    * Empirical observations on basic factors
        * 어떻게 basic factor들이 안정성에 영향을 주는지 연구했다.
        * Batch size
            * ViT는 기본적으로 연산량 측면에서 무겁게 설계됐다.
            * 따라서 배치 사이즈도 더 클수록 유리하다.
            * Fig 1
            * 1k, 2k 배치는 kNN 커브가 완만하게 올라간다.
            * 4k 배치에 도달하면 이제 학습이 불안정해진다.
            * 중간중간에 구덩이인 'dips'가 발생한다.
            * 4k 배치는 2k 배치와 최종 acc 측면에서 거의 차이가 없어 보인다.
            * 하지만 다음 섹션에서 나오는 테스트를 해보면 문제가 발견됐다.
            * 6k 배치를 보면 왜 ViT가 골치아픈 모델인지 볼 수 있다.
            * 구덩이가 매우 깊고 많은 것을 볼 수 있는데, 저자들의 가정은 학습을 하다가 이번 로컬 옵티멈을 빠져나오기 위해 부분적으로 재시작 해버리고 'jump out'하여 새로운 전략을 모색한다는 것이다.
            * 보통 이런 경우 발산하여 아예 망해버리고 acc가 낮게 나와야 한다.
            * 그런데 결과를보면 acc 측면에서 3%로 작은 차이가 난다.
        * Learning rate
            * Fig 2
            * 여기서 best lr은 1.0e-4 이다.
            * lr이 작을 때는 학습이 더 안정적이지만 언더피팅 된다.
            * lr이 커지면 불안정성을 보인다.
            * 학습이 진행되며 dips가 더 자주 깊게 발생한다.
        * Optimizer
            * 생략
    * A trick for improving stability
        * 개요
            * 간단한 트릭을 이용해 안정성을 향상시킨다.
            * Fig 4
            * 앞서 관찰한 dips가 발생하는 이유는 갑작스러운 gradient 업데이트인 'spike' 때문이다.
            * 전체 레이어를 모두 관찰해본 결과 가장 첫 레이어(patch projection)에서 많이 발생했다.
            * 저자들은 이를 방지하기 위해 patch projection layer를 고정했다.
            * 구현은 random patch projection을 만들어 값을 고정하였다.
        * Comparisions
            * Fig 5
            * patch projection을 학습하는 경우 vs 고정하는 경우 비교다.
            * 고정한 경우 매우 안정적이고 스무스하게 학습된다.
            * 모든 lr 조건에서 더 좋은 최종 acc가 나왔다.
            * 이걸 다른 프레임워크인 SimCLR, BYOL에 적용해봤는데 효과가 있었다.
<br><br>

### [결과 분석]
* Implementation details
    * 
* Experimental results
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
* NLP랑 다르게 masked auto-encoding 보다 contrastive learning이 낫다.
    * 이 논문 전 까지는 그랬음.
* ViT vs CNN
    * inductive bias가 적다는 점이 ViT의 가장 큰 장점.
    * ViT는 포지션 임베딩을 지우더라도 정확도가 비슷하다.
    * ViT가 포지션 정보를 충분히 반영하지 않는다는 문제를 내포하는 것이다.
<br><br>


