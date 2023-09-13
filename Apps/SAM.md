# Segment Anything
* KIRILLOV, Alexander, et al. Segment anything. arXiv preprint arXiv:2304.02643, 2023.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* NLP에는 GPT 모델 같은 혁신적인 모델이 있다.
* 그러나 CV에는 그런 모델이 없다.
* CV의 Foundation Model을 디자인 해보자. (zero-shot transfer)
* SAM을 출시하고, CV의 foundation model 연구 촉진을 하자.
* 이 모델을 학습시키기 위한 초거대 segmentation dataset을 만들어보자.
<br><br>

### [기존 문제점]
* NLP의 거대한 capability는 prompt engineering으로 구현되는 편이다.
* 하지만 CV에는 다양한 문제점이 있고, 특히 풍부한 train dataset이 부족하다.
<br><br>

### [해결 아이디어]
* promptable 모델을 설계하여 광범위한 dataset으로 pre-training 하자.
    * 3가지 메인 구성요소 정의(task, model, data)
    * 충분히 보편적인 task, flexible prompting을 지원하는 model, 거대한 원천 data
* Task
    * Task
        * NLP의 prompt 아이디어를 segmentation의 상황에 맞게 해석 하자.
        * 어떤 prompt가 주어져도 valid한 segmentation mask를 리턴하는 것.
        * Valid란 prompt가 여러가지로 해석될 수 있더라도 output 중 1개는 반드시 타당해야 한다.
        * (LLM이 다중의미의 prompt에도 일관성 있는 대답을 하는 것처럼)
        * 이런 task가 자연스러운 pre-training 알고리즘으로 이끌고, downstream에 대하여 보편적으로 작용하게 한다.
    * Pre-training
        * 각 샘플에 대하여 prompt의 시퀸스가 주어진다. (points, boxes, masks)
        * 모델의 마스크 예측과 ground truths와 비교한다.
        * 이 방법을 interactive segmentation에 적용한다.
        * (interactive segmentation: 사용자 클릭을 사용하여 CNN을 트레이닝 시킴)
    * Discussion
        * Prompting and composition은 모델이 확장하는 방법에 있어 매우 파워풀한 툴이다.
        * 복합적인 시스템 설계가 더 다양한 downstream 작업을 가능하게 할 것으로 기대할 수 있다.
* Model
    * Overview
        * 아키텍쳐는 크게 3가지로 나눌 수 있다.
        * image encoder + flexible prompt encoder + fast mask decoder
    * Image encoder
        * 이미지에서 임베딩을 추출한다.
        * MAE로 프리 트레이닝된 ViT-H/16 이다.
        * MAE(Masked AutoEncoder)는 높은 scalability와 강력한 pre-training이 가능하게 해준다.
        * 인풋 이미지 : (1024 * 1024) size * (16 * 16 * 3) channels
        * 패치 사이즈 = 16이며, 채널에 (1x1 Conv) (3x3 Conv) (Norm) 진행.
        * 아웃풋 임베딩 : (64 * 64) patches * 256 dimensions
    * Prompt encoder
        * 프롬프트에서 임베딩을 추출한다.
        * 프롬프트는 2가지 종류. Sparse(points, boxes, text), Dense(masks).
        * 모든 케이스는 기존에 있는 임베딩을 추출하는 알고리즘을 활용한다.
        * points, boxes는 positional encoding(해당 점의 위치 + 피사체와 배경을 구별하게 학습된 임베딩)를 추출 from 푸리에 피쳐 머시기 논문
        * free-form text는 off-the-shelf text encoder로 추출 from CLIP 논문
        * masks는 convolution 레이어를 통과시켜 16배 작게 만들고 이미지 임베딩과 element-wise로 더함 from 일반적인 segmentation 메서드
    * Mask decoder
        * 이미지 임베딩과 프롬프트 임베딩을 효율적으로 매핑해 아웃풋 마스크를 만든다.
        * 총 2번의 디코딩(self-attn, cross-attn, MLP, corss-attn)이 이루어진다.
        * 0: 프롬프트 임베딩에 output token embedding을 부착한다. (클래스 토큰 개념) 그리고 지금부터 프롬프트 임베딩을 토큰이라 부른다.
        * 1: self-attention 레이어를 통해 토큰에서 representation을 추출한다.
        * 2: cross-attention 레이어를 통해 토큰(=Q)에서 이미지 임베딩(=K, V)을 사용해 representation을 추출한다. (이때는 토큰에 가까울 것임)
        * 3: point-wise MLP가 토큰 즉 프롬프트를 각 토큰에 대해 차원 간 업데이트를 한다. (GAP 레이어와 비슷한 역할이나 차원을 줄이진 않음. 더 복잡한 임베딩을 계산.)
        * 4: cross-attention 레이어를 통해 이미지 임베딩(=Q)에서 토큰(=K, V)을 사용해 representation을 추출한다. (이때는 이미지 임베딩에 가까울 것임)
        * 5: 2개의 transposed Conv layer를 사용해서 이미지 임베딩을 4배로 키운다.
        * 6-1: cross-attention 레이어를 통해 토큰(=Q)에서 이미지 임베딩(=K, V)을 사용해 representation을 추출한다. (이때는 토큰에 가까울 것임) 여기에 들어가는 토큰은 최종 크로스 어텐션 직전의 토큰이고, 이미지 임베딩은 최종 이미지 임베딩이다.
        * 6-2: 6-1의 output을 small 3-layer MLP에 통과시킨다. (업스케일링된 이미지 임베딩과 채널 디멘션 매칭을 위함)
        * 7: 5의 결과와 6의 결과를 spatially point-wise product하여 최종 마스크를 예측한다. (5는 업스케일링된 이미지 임베딩, 6은 dimension이 매칭된 토큰)
    * Ambiguity-aware
        * single input prompt는 이 상황에서 ambiguous를 일으킬 수 있다.
        * prompt의 정확한 의도가 모호하여 일관적인 답을 못낼 가능성이 있는 것.
        * 저자들은 3개의 output tokens를 사용하여 multiple masks를 예측하게 했다. (Whole, part, subpart)
        * 학습하는 동안에 3개의 loss를 항상 계산하되, backpropagation은 가장 낮은 loss로만 진행한다.
        * small head 1개가 masks의 랭킹을 계산한다.
        * 각 마스크가 object를 얼마나 커버하는지 IoU를 계산한다.
    * Losses
        * focal loss와 dice loss에 weight를 줘서 20:1 비율로 더함.
        * 여기서 focal loss는 각 픽셀이 segmentation의 클래스를 맞췄는지 classification과 관련되어 있고, dice loss는 segmentation의 영역이 overlaping된 양과 관련되어 있다.
    * Training algorithm
        * Interactive segmentation으로 셋업 되었다.
        * 유저가 모델에 feedback을 주는 작업을 자동화 설계 했다.
        * 용어로 foreground는 피사체이고, background는 배경 영역이다.
        * 1: 전체 이미지에서 랜덤하게 foreground point가 선택된다.
        * 2: 모델이 첫 prompt(=point)에 대하여 mask를 예측한다.
        * 3: error region에서 균일하게 후속 point가 선택된다.
        * 4-1: 새 point가 foreground 위에 있으면 false negative 다.
        * 4-2: 새 point가 background 위에 있으면 false positive 다.
        * 5: 새 point가 1의 foreground point이며 이 작업을 자동으로 반복한다.
* Data
* 학습에 사용될 데이터(이미지, 레이블 마스크)를 만드는 데이터 엔진이 구동된다.
* 3가지 스테이지의 데이터 엔진이 구동되며, 최종 목표는 완전 자동화된 데이터 엔진이다.
    * Assisted-manual stage
        * Annotator들은 foreground/background 포인트 클릭을 통해 마스크를 레이블링 한다.
        * SAM로 구동되는 브라우저 형태의 툴을 사용할 수 있다. 마스크를 브러쉬와 지우개 툴으로 더 섬세하게 보정한다.
        * SAM은 해당 컨셉으로 6번 학습된다.
    * Semi-automatic stage
        * 마스크의 다양성을 향상시키기 위해 반자동으로 구동하는 스테이지.
        * 모델은 자동으로 confident 마스크를 감지하고 마스크 밑작업을 한다.
        * Annotator들은 결과를 보고 추가로 마스크를 칠한다.
    * Fully automatic stage
        * 데이터셋의 양을 폭발적으로 늘리기 위해 완전 자동화한다.
        * 이를 위해서 2가지 주요 성능향상이 필요하다.
        * 1: 충분한 양의 마스크 -> 모델이 valid mask를 예측할 수 있게 한다.
        * 2: 모호성 인지(ambiguity-aware) -> 모델이 mask 세트(서브파트, 파트, 전체)를 예측할 수 있게 한다.
        * 이미지에 32x32개의 포인트를 뿌리고 각 포인트에 대한 마스크를 모델이 예측하도록 한다.
        * IoU 모듈이 마스크의 confident와 stable을 계산한다.
        * NMS를 이용해 중복된 마스크를 제거한다.
* Zero-Shot Transfer Experiments
* 총 6가지 종류의 downstream을 zero-shot으로 실험했다.
* (작업명, 작업 설명, 알고리즘 설명, 기존 모델과 비교)
    * 1: Single point segmentation
        * 동일한 작업
        * 동일한 알고리즘
        * 기존의 모델(RITM)보다 좋은 성능
    * 2: Edge detection
        * 경계선 검출
        * 16x16개의 포인트 -> 포인트에 대한 마스크 예측 -> NMS -> Sobel
        * 기존의 모델(EDETR)보다 약간 낮은 성능
    * 3: Object proposals
        * 가능성이 높은 일부 객체의 위치 제안
        * 1000개 이상의 마스크 생성 -> IoU와 stability를 기준으로 선택
        * 기존의 모델(ViTDet-H)과 유사한 성능
    * 4: Instance segmentation
        * object detection 후 해당 object를 segmentation
        * ViTDet로 object detection -> box를 prompt로 SAM 구동
        * 기존의 모델(ViTDet-H)보다 약간 낮은 성능
    * 5: Text-to-mask
        * 텍스트를 입력하면 해당 텍스트의 객체를 segmentation
        * CLIP으로 text 임베딩 생성 -> text를 prompt로 SAM 구동
        * 기존 모델 없음
    * 6: Ablations(생략)
<br><br>

### [추가로 볼 레퍼런스]
* interactive segmentation
* Fourier features let networks learn high frequency functions in low dimensional domains
* CLIP
* focal loss, dice loss
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* 
<br><br>


