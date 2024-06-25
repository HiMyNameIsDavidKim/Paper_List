# Context Autoencoder for Self-Supervised Representation Learning
* CHEN, Xiaokang, et al. Context autoencoder for self-supervised representation learning. International Journal of Computer Vision, 2024, 132.1: 208-223.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 새로운 masked image modeling(MIM)인 CAE를 제시한다.
* representation space에서 인코더를 학습해보자.
* task: (masked representation을 예측 -> masked 패치를 reconstruction)
* 아키텍처: (인코더 -> regressor -> 디코더)
* 이 설계 구조를 통해 인코더를 reconstruction에서 완전히 분리하고자 한다.
<br><br>

### [기존 문제점]
* MIM은 이미지의 몇몇 패치를 가리고 가린 부분을 예측하며 인코더를 학습시킨다.
* 기존의 전형적인 MIM 모델들은 2가지 task가 섞여있다.
* (인코더가 representation을 학습) + (masked 패치를 reconstruction)
* 최근 연구인 MAE는 인코더-디코더 구조를 최적화하여 두 task를 완전히 분리했다.
* 하지만 여전히 representation quality가 한계가 있다.
<br><br>

### [해결 아이디어]
* CAE
    * Fig 1
    * 인코더-regressor-디코더 구조의 CAE를 제시한다.
    * 인코더는 오직 visible 패치만 받아 representation을 학습한다.
    * regressor는 masked 패치의 representation을 예측만 한다.
    * 디코더는 regressor의 결과를 받아 masked 패치를 reconstuction 한다.
    * representation space에서 예측하는 것의 장점은 다음과 같다.
    * visible space가 아니라 같은 space에서 예측하기 때문에 더 그럴듯한 semantic 추측이 가능하다.
    * 따라서 더 방대한 범위의 semantics를 인코딩할 것이라고 예상했다.
    * 인코딩 작업과 이미지 완성 작업이 분리되었기 때문에 인코더는 정확히 인코딩 작업만 집중해서 학습할 수 있다.
    * representation끼리 매핑하는 작업은 regressor가 한다.
* 기존 모델과 비교
    * Fig 2
    * 윗부분은 MAE의 아키텍처를 표현한 것이다.
    * MAE는 디코더가 (masked 패치 예측)과 (reconstruction) 두 task를 한다.
    * masked 패치 representation 예측을 하는 명시적인 모듈이 없다.
    * 쉽게 설명하면 디코더가 representation, pixel 두 space 작업을 모두 한다.
    * 아랫부분은 data2vec과 iBOT의 아키텍처를 표현한 것이다.
    * 마찬가지로 masked 패치 representation 예측을 하는 명시적인 모듈이 없다.
    * 그리고 타겟 representation이 전체 패치(visible+masked)를 다 보고 추출되는 구조다.
    * (CAE는 visible 패치만 인코딩, masked 패치만 인코딩, 따로 하는 방식)
* Architecture
    * Fig 1
    * 전체 이미지를 패치로 만든다.
    * 패치를 2세트로 나눈다. (visible 패치 X_v, masked 패치 X_m)
    * 인코더 : visible 패치만 인코더로 넣는다.
    * regressor : masked 패치의 representation 예측
    * (인코더가 masked 패치만 인코딩한 representation과 정렬)
    * 디코더 : masked 패치 reconstruction
    * 인코더
        * 인코더 F는 visible 패치 X_v를 인풋으로 받는다.
        * 포지션 임베딩 P_v를 더해주고 인코더 블럭에 들어간다.
        * 아웃풋은 latent representation Z_v이다.
        * MAE처럼 회색 처리된 masked 패치가 같이 들어가지 않는다.
    * Regressor
        * regressor H는 인코더의 아웃풋 Z_v를 인풋으로 받는다.
        * masked 패치의 representation Z_m을 예측한다.
        * Q_m : initial queries, 어떤 패치가 masked 인지에 대한 정보
        * Q_m을 참고해서 representation들을 정렬한다.
        * (저자는 모든 모듈이 명시적이고 한가지 task만 한다고 했는데... 이 regressor가 처리하는 일이 많고 복잡하다. 하나도 간단하지 않다. 과대광고 멈춰주길 바란다...ㅇㅁㅇ)
    * 디코더
        * 디코더 G는 regressor의 아웃풋 Z_m을 인풋으로 받는다.
        * 타겟 Y_m을 reconstruction 한다.
    * Objective function
        * 마스킹은 BEiT처럼 랜덤하게 아무 패치나 무작위로 한다.
        * 마스킹 되는 양은 절반이다. (패치 196개 중 98개)
        * 타겟 Z_m을 구하기 위해 masked 패치 X_m만 인코더에 따로 넣는다.
        * 이 인코더는 visible 패치를 처리하는 인코더와 동일한 인코더 이다.
        * 타겟 Y_m은 픽셀 수준인줄 알았는데 그건 또 아니다.
        * d-VAE로 reconstruction 타겟 Y_m을 만든다.
        * 이건 패치를 각각 벡터 space로 변환한 것이다. (pixel값 아님)
        * Loss function은 reconstruction loss와 align loss로 구성된다.
        * Eq 1으로 나타낼 수 있으며, 실험결과 람다는 2가 가장 좋았다.
<br><br>

### [결과 분석]
* Training details
    * Pre-training: AdamW, epochs 300/800/1600, batch size 2048, learning rate 1.5e-03, cosine decay, warm up
    * Linear probing: LARS, epochs 90, batch size 16384, learning rate 6.4, no decay, warm up
    * Fine-tuning: AdamW, epochs 100, batch size 4096, learning rate 8e-03, layer wise decay, warm up
    * Attentive-probing: (생략)
* Evaluation
    * Table 1
    * 아스타가 붙은 CAE는 d-VAE 대신 DALL-E를 토크나이저로 사용했다.
    * 대부분의 모델이 있는 ViT-B를 기준으로 보자.
    * Linear probing은 contrastive 방식인 MoCo와 DINO가 매우 높다.
    * MIM 방식 보다 contrastive 방식이 높은 이유는 1000개 클래스에 대한 vocabulary를 형성하는데 집중하기 때문이다.
    * 그래도 CAE가 MIM 방식 중에서 가장 높은 accuracy를 기록했다.
    * Attentive probing은 contrastive 방식과 견줄 정도로 CAE도 높다.
    * Fine-tuning의 경우 모든 모델 중에서 가장 높았다.
* Downstream tasks
    * Table 2
    * Semantic segmentation
    * ViT-B에서 모든 모델 중에서 가장 좋았다.
    * ViT-L도 모든 모델 중에서 가장 좋았다.
    * Table 3
    * Object detection
    * ViT-B에서 모든 모델 중에서 가장 좋았다.
    * ViT-L도 모든 모델 중에서 가장 좋았다.
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


