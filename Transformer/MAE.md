# Masked Autoencoders Are Scalable Vision Learners
* HE, Kaiming, et al. Masked autoencoders are scalable vision learners. In: Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 2022. p. 16000-16009.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* MAE는 CV분야의 scalable한 self-supervised 모델이다.
* 큰 모델을 효율적이고 효과적으로 학습해보자.
* generalize가 잘 되는 높은 capacity를 가진 모델에 대하여 접근해보자.
<br><br>

### [기존 문제점]
* NLP의 BERT 모델에서 masked autoencoder 메서드를 잘 사용하고 있으나, CV에는 비교적 성능이 떨어진다.
* NLP와 CV의 어떤점이 MAE에 차이점을 야기하는지 생각해보자.
    * CNN과 BERT의 architecture 차이(ViT 등장으로 어느정도 해결)
    * Information density
    * Autoencoder의 Decoder 부분
<br><br>

### [해결 아이디어]
* Masking a high portion
    * 언어는 인간이 만든 시그널이고, 이미지는 자연이 만든 시그널이다.
    * 언어는 이미지에 비해서 높은 밀도의 정보를 가지고 있다.
    * NLP 모델은 오직 적은 수의 단어만 예측하는데, 그렇다고 해서 CV에서 적은 양의 mask를 해선 안된다. 정보 밀도의 수준이 다르기 때문이다.
    * 이 전략은 정보 과잉을 해결해주고, 이미지 전체를 보는 능력을 요구한다.
* Masking ramdom patches
    * CV에서는 디코더가 pixel 재구성 작업을 하는데 이것은 recognition에 비해 low semantic level의 작업이다.
    * NLP에서는 디코더가 missing word 재구성 작업을 하는데 이것은 high semantic level의 작업이다.
    * 따라서, pixel이 아닌 patches를 재구성 하게 만들고, token(=포지션)을 가려주어야 비슷한 수준의 작업이 된다.
* Approach
    * Masking
        * ViT처럼 MAE도 이미지를 패치로 쪼갠다.
        * 높은 마스킹 비율로 랜덤 샘플링하면 정보 과잉을 줄일 수 있다.
        * 작업이 주변 패치에 의해 너무 손쉽게 풀리는 경우를 방지한다.
    * MAE encoder
        * MAE의 인코더에는 ViT와 동일하지만 오직 마스킹되지 않은 패치 'sub set'만 인풋된다.
        * 인풋과 아웃풋 모두 전체 사진에서 작은 포션(약 25%)의 패치 세트로 구성된다.
        * 이로써 계산량과 메모리의 일부만 사용하면서 매우 큰 크기의 인코더를 학습시킬 수 있다.
    * MAE decoder
        * MAE 디코더에는 마스킹되지 않은 패치와 마스킹된 패치 'full set' 모두 인풋된다.
        * full set 모든 패치(=토큰)에 포지셔널 임베딩을 더해준다.
        * MAE의 디코더는 오직 pixel reconstruction task를 하는 pre-training에서만 사용된다.
        * 인코더보다 작고 가볍고 얕게 설계하여 실험한다. (lightweight decoder)
    * Reconstruction target
        * 각 마스크 패치의 pixel value를 예측한다.
        * 디코더의 아웃풋은 이미지의 형태를 재구성한다.
        * loss function으로는 pixel space에서 MSE를 계산했다.
        * BERT처럼 loss를 계산할 때 오직 마스킹된 패치만 계산했다. (마스킹 안된 패치는 loss 계산 시 사용 안했다는 뜻)
<br><br>

### [결과 분석]
* ImageNet Experiments
    * Baseline: ViT-L/16
        * 저자들의 강력한 regularization recipe로 기존 논문보다 좋은 결과가 나왔다.
        * pre-training은 ImageNet-1K로 reconstruction task를 self-supervised learning 했고, fine-tuning은 ImageNet-1K로 recognition task를 supervised learning 했다.
        * MAE는 오직 50 epochs만 fine-tuning 했다. (반면에 ViT-L/16은 200 epochs)
        * fine-tuning과 linear probing은 별도의 레시피를 사용한다.
    * Masking ratio
        * 마스킹 비율은 75%가 fine-tuning과 linear probing 모두에서 가장 좋았다.
        * 이는 BERT의 전형적인 비율인 15%와 크게 대조된다.
        * fine-tuning과 linear probing는 비율에 따라 다른 트렌드를 보인다.
    * Decoder design
        * 디코더의 depth와 width는 fine-tuning에서는 크게 중요하지 않았다.
        * 따라서 가볍게 만들수록 이득이고 저자들은 최대한 가볍게 만들었다.
        * 디코더의 depth가 얕아질수록 인코더는 reconstruction task에 더 특화되는 경향을 보이고 recognition은 영향이 없다.
    * Mask token
        * 인코더에 마스크 토큰을 넣냐 안넣냐에 따라서 약 3.3배의 FLOPs 차이가 있다.
        * 따라서 저자들은 마스크 토큰을 인코더에서는 안넣고 효율적으로 학습했다.
        * 이상한 점은 linear probing의 accuracy가 크게 좋아졌다는 점이다.
    * Augmentation
        * crop과 random-size를 통해 accuracy가 조금 올라갔지만 안써도 크게 차이가 없다.
    * Schedule
        * 이 논문에 나온 결과는 기본적으로 800 epochs 까지 진행한 결과이다.
        * 하지만 1600 epochs 까지 진행해도 saturation이 되지 않는 특징을 보인다.
    * Compare with previous results
        * MAE(fine-tuning)과 다른 self-supervised ViT 모델들의 결과를 비교했다.
        * MAE는 448 size로 fine-tuning 시 87.8% 의 결과가 나왔다. (best)
        * 기존 모델들에 비해서 근소하게 앞서는 결과가 나왔다.
        * 이 모델은 오직 바닐라 ViT에 기반한 것이며, 더 발전된 모델은 더 좋은 결과를 얻을 수도 있다. (ex. BEiT, DeiT, DaViT, etc.)
* Transfer Learning Experiments
    * Object detection and segmentation
        * Mask R-CNN의 VGG backbone을 MAE ViT로 교체했다.
        * COCO 데이터셋에 대하여 end-to-end 학습했다.
        * object detection의 결과는 box Average Precision으로 평가했고, segmentation의 결과는 mask Average Precision으로 평가했다.
        * 두 task에서 모두 기존 모델에 비해 근소하게 좋은 결과가 나왔다.
    * Semantic segmentation
        * UperNet의 FPN backbone을 MAE ViT로 교체했다.
        * ADE20K 데이터셋에 대하여 학습했다.
        * 기존 모델에 비해 근소하게 좋은 결과가 나왔다.
    * Classification task
        * ImageNet 이외의 다른 classification 데이터셋인 iNaturalist와 Places에 대하여 transfer learning 했다.
        * iNat에서는 모델이 커질수록 accuracy가 좋아지는 것을 통해서 MAE가 strong scaling 특성이 있다는 것을 확인했다.
        * Places에서는 기존 모델보다 근소하게 좋은 결과가 나왔다.
<br><br>

### [추가로 볼 레퍼런스]
* Accurate, large minibatch SGD: Training ImageNet in 1 hour
    * lr, warm up 관련.
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* capability
    * 모델이 얼마나 복잡한 함수를 학습하거나 모델이 어떤 유형의 작업을 수행할 수 있는지에 대한 능력
    * 더 복잡한 패턴이나 관계를 학습
* capacity
    * 모델 내부의 매개변수(parameter)의 개수나 모델의 구조의 용량
    * 더 복잡한 함수를 표현
    *  generalize가 잘 된다.
* linear probing
    * 사전 학습된 모델의 출력 특징은 고정하고 출력된 특징을 기반으로 학습.
    * MLP head만 weight를 새로 학습하는 것.
    * 사전 학습된 모델 전체를 다시 학습하는 fine-tuning과는 다른 방법이다.
    * 더 빨리 학습시킬 수 있으나, 성능이 떨어질 수 있다.
<br><br>


