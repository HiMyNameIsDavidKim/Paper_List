# V-JEPA: Latent Video Prediction for Visual Representation Learning
* BARDES, Adrien, et al. V-JEPA: Latent Video Prediction for Visual Representation Learning. 2023.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* LLM에 masked modeling 방법을 사용해 효율적으로 학습시켜보자.
* 비디오에서 SSL으로 시공간적 마스킹 영역을 예측하는 V-JEPA 모델을 제시한다.
* 레이턴트 비디오 예측 전략은 다운스트림에서 추가로 모델을 최적화할 필요가 없다.
* 비디오 학습이 이미지 학습 보다 모션을 이해시키는데 유리하다.
<br><br>

### [기존 문제점]
* 인지 이론을 활용한 SSL은 NLP에 큰 발전을 기여했다.
* 비전 분야 중 특히 비디오에 어떻게 적용해야할지 아직 특출난 방법이 없다.
<br><br>

### [해결 아이디어]
* Overview
    * JEPA 기법과 비디오 데이터셋을 활용해보자.
    * 비디오를 시공간 마스크로 가리고 이 부분을 예측하며 비주얼 인코더를 학습한다.
* Background
    * Weakly supervised learning
        * 웹의 이미지와 캡션 텍스트로 비주얼 인코더 학습.
        * CLIP, VideoBERT, VideoCLIP, MERLOT, VATT, InternVideo, etc.
    * SSL from images
        * 사람이 만든 주석이 없이 자기지도학습.
        * SimCLR, MSN, MAE, Data2Vec, I-JEPA, DINOv2, etc.
    * SSL from videos
        * 이미지를 넘어 시간 차원이 부여된 비디오로 학습.
        * MC-JEPA, VITO, VideoMAE, VideoMAEv2, iMAE, etc.
* Methodology
    * I-JEPA
        * 1개의 context으로 4개의 target을 추상적인 레이턴트 스페이스에서 예측한다.
        * 중요한 모듈은 3개다. (context encoder, target encoder, predictor)
        * 저자들은 이 아키텍처를 비디오와 마스킹에 적용해 V-JEPA를 구상했다.
    * Architecture
        * backbone으로 ViT를 사용한다. (ViT-L or ViT-H)
        * 이 ViT는 스탠다드 트랜스포머 블럭에 joint space-time attention을 적용한 것이다.
    * Input
        * ViT는 1D 시퀀스를 인풋으로 받는다.
        * 3D 비디오를 1D 토큰 시퀀스로 바꿔야 한다. (Fig 4)
        * sequence
            * 3D Conv filter(filter d, size (2, 16, 16), temporal stride 2, spatial stride 16) -> output tensor 모양 (8, 14, 14, d)
            * 3D sin-cos 포지션 임베딩 더해주기.
            * flatten 사용해서 1D로 펼쳐주기.
            * 최종 시퀀스 모양 (1568, d)
        * video
            * 각 인풋 비디오에서 stride 4로 두고 랜덤하게 16프레임의 클립을 추출.
            * 총 64프레임으로 약 2초 정도 되는 길이의 클립.
            * spatial 사이즈를 224로 통일.
            * 최종 비디오 모양 (16, 224, 224, 3)
    * 3D multi-block masking
        * 블럭 마스킹 전략을 간단하게 3D로 확장한다.
        * 공간적으로 끊어지지 않은 비디오가 주어지면 몇개의 블럭으로 샘플링한다.
        * 이 비디오들은 종횡비가 다양하며, 이를 모두 커버하는 1개의 공간적 마스크를 구한다.
        * 이 공간적 마스크를 전체 시간적 차원으로 확장하여 최종 마스크가 된다.
        * V-JEPA에서는 2가지 마스크(short-range, long-range)를 사용한다.
        * short-range mask(8개 랜덤 타겟 블럭, 전체 이미지의 0.15배 크기)
        * long-range mask(2개 랜덤 타겟 블럭, 전체 이미지의 0.7배 크기)
        * 두 마스크 모두 종횡비는 랜덤하게 결정(0.75~1.5)
        * 이 마스크의 최종 결정은 모든 마스크의 면적을 겹쳤을때 전체 이미지의 90%는 차지해야 한다는 규칙을 따른다.
        * 다시말해 context 인코더에 인풋으로 들어가는 것은 10% 밖에 안된다.
    * Patch-level loss
        * 시퀀스 길이
            * 먼저 비디오 클립과 비디오 마스크를 샘플링 한다.
            * 비디오 클립은 1D 토큰 시퀀스이고 길이 L은 1568 이다.
            * 마스크의 시퀀스 길이는 M, 언마스크의 시퀀스 길이는 N, N = L-M 이다.
        * context representation
            * 비디오 클립에 마스킹을 하고(x_N), context 인코더에 넣는다.
            * z_N = E(x_N) 으로 표기.
        * predict
            * context 인코더의 아웃풋(z_N)을 predictor에 넣는다.
            * 마스크 토큰(m)을 같이 넣는다.
            * s_M = P(z_N, m_M) 으로 표기.
        * target representation
            * 마스킹 안된 전체 비디오 클립(x_L)를 target 인코더에 넣는다.
            * s_L = E(x_L) 으로 표기.
        * Loss
            * predictor의 s_M과 target 인코더의 s_L으로 L1 loss를 계산한다.
            * Eq 1 으로 표기.
    * Multi-mask prediction
        * I-JEPA나 다른 모델과 마찬가지로 타겟 블럭을 여러개 예측한다.
        * 1번의 iter에서 타겟 representation을 1번만 계산하므로 효율적이다.
    * Collapse prevention mechanism
        * representation collapse라는 개념이 있다.
        * 인코더가 입력과는 관계없이 계속 일정한 값을 출력하는 현상이다.
        * 이를 피하기 위해 target 인코더의 파라미터 업데이트는 gradient 방식이 아니라 context 인코더의 exponential moving average 방식을 사용한다.
        * 이는 I-JEPA에서도 사용했던 방법이다.
<br><br>

### [결과 분석]
* Experimental setting
    * 
* Eval on video task
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


