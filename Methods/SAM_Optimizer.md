# Sharpness-aware Minimization for Efficiently Improving Generalization
* FORET, Pierre, et al. Sharpness-aware minimization for efficiently improving generalization. arXiv preprint arXiv:2010.01412, 2020.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 모델이 무거워짐에 따라 loss를 잘 다루는 지가 일반화에 영향을 준다.
* loss landscape의 기하학적 구조를 활용한 새로운 optimization 메서드를 연구한다.
* loss landscape의 sharpness와 일반화를 연결해서 연구해보자.
* 주변과 비교해 uniformly하게 낮은 loss를 찾는 새로운 optimizer를 연구해보자.
<br><br>

### [기존 문제점]
* 학습 loss landscape가 시간이 갈수록 복잡해진다.
* 로컬 옵티마 글로벌 옵티마도 여러개가 있다.
* 현재 optimizer 들은 여전히 효율성, 확장성 문제를 갖고 있다.
<br><br>

### [해결 아이디어]
* SAM Optimizer overview
    * Sharpness-Aware Minimization
    * 일반화 성능에 있어서 효율적이고 효과적이며, scalable 하다.
    * loss의 value와 sharpness를 동시에 최소화한다.
    * Fig 1
    * 주변 값을 함께 고려해 uniformly하게 낮은 loss value를 찾는다.
* Sharpness-Aware Minimization
    * 공식화
        * training 데이터셋 S = Σ(x_i, y_i)
        * 일반화가 잘된 distribution D
        * 모델의 파라미터 w, loss function l
        * training 데이터셋 loss L(w) = Σl(w, x_i, y_i)
        * L_S(w)를 최적화해서 L_D(w)를 추정해야 한다.
    * L_S(w)는 전형적으로 non-convex 한 형태이다.
    * 여러개의 local minima와 거의 비슷한 값의 global minima가 많다.
    * 기존 방법: 더 낮은 loss를 찾는다.
    * 제안 방법: 더 낮은 loss이면서 곡률이 작은 loss를 찾는다.
    * 곡률이 작으면 주변의 모든 값이 낮은 'uniformly low loss' 이다.
    * 공식 유도
        * Eq 1
            * where 왼쪽: 원래 loss 함수를 최소화 하는 optimizer 공식 (+ L2 norm)
            * where 오른쪽: ϵ만큼 파라미터를 변화했을 때, 손실의 날카로움을 줄이는 공식
            * (w+ϵ) 범위에서 가장 큰 loss를 계산하고 그 값이 (w)에서 loss와 차이가 작게 한다.
            * 여기서 ρ는 주변으로 취급하는 반경
        * Eq 2
            * classical dual norm problem 을 사용한다.
            * norm은 벡터의 크기를 측정하는 함수이다.
            * dual norm은 주어진 norm의 반대 방향 norm 이다.
            * 벡터의 dual norm을 사용하면 최대값으로 움직이는 방향을 찾을 수 있다.
            * 따라서 최대값으로 움직이는 ϵ(w)를 계산한다.
        * Eq 3
            * 결론적으로 날카로움을 고려한 w+ϵ(w)을 사용해서 loss 함수를 최소화 한다.
        * Algo 1, (while 루프)
            * w의 그라디언트를 계산한다.
            * Eq 2에 w의 그라디언트를 대입해 ϵ(w)를 계산한다.
            * w+ϵ(w)의 그라디언트를 계산한다.
            * 이 그라디언트가 Eq 3에 의해 SAM objective 이다.
            * 웨이트를 업데이트 한다.
    * 파이토치 관점
        * first step: (w로 그라디언트 계산) -> (Eq 2에 넣고 ϵ(w) 계산) -> (w+e(w)로 웨이트 업데이트)
        * second step: (w+e(w)로 그라디언트 계산) -> (w로 웨이트 복원) -> (SAM objective로 웨이트 업데이트)
        * w+e(w)에서의 그라디언트가 필요해서 웨이트 업데이트 후 그라디언트 계산을 해야한다.
        * w+e(w)에서 바로 업데이트하면 2번 업데이트 하는 것이므로 복원이 필요하다.
        * ϵ(w)는 w의 그라디언트가 있어야 구할 수 있다.
        * SAM objective는 w+e(w)의 그라디언트가 있어야 구할 수 있다.
<br><br>

### [결과 분석]
* Image Classification
    * 주변 반경 ρ = {0.01, 0.02, 0.05, 0.1, 0.2, 0.5}
    * Table 1
    * cifar10, cifar100
    * WideResNet, PyramidNet
    * 모든 케이스에서 SAM을 사용할 경우 모델의 성능이 더 좋아졌다.
    * Table 2
    * ImageNet
    * ResNet-50, 101, 152
    * 모든 케이스에서 SAM을 사용할 경우 퍼포먼스 성능이 더 좋아졌다.
* Finetuning
    * 큰 데이터셋 프리 트레이닝 -> 관련 작은 데이터셋 transfer learning
    * 프리트레이닝은 SAM을 쓴게 아니라 기존 모델을 불러와서 사용한다.
    * EffNet-b7 (ImageNet), EffNet-L2 (JFT-300M)
    * SOTA (ImageNet): EffNet, BiT-M, ViT 등 다양한 모델 중 acc 가장 높은 모델
    * 모든 케이스에서 SAM을 사용할 경우 일반화 성능이 좋아졌다.
    * 몇몇 케이스에서는 기존 SOTA의 성능을 뛰어넘는 결과를 가져오기도 했다.
* 내 생각
    * 이 평가방식은 학습한 데이터셋에 대한 generalization 성능을 평가하는 것이다.
    * 어떤 데이터셋에 대해서도 robustness 하다를 평가하기 위해서는 다른 방법을 사용해야 한다.
    * pre-training에서 SAM 사용하고 linear probing 하는게 더 정확한 결과로 생각된다.
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


