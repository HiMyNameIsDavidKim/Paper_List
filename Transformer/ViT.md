# AN IMAGE IS WORTH 16X16 WORDS:TRANSFORMERS FOR IMAGE RECOGNITION AT SCALE
* DOSOVITSKIY, Alexey, et al. An image is worth 16x16 words: Transformers for image recognition at scale. arXiv preprint arXiv:2010.11929, 2020.
* ICLR 2021
<br><br>

## [`논문 요약`]

### [저자의 의도]
* CNN에 의존하지 않는 pure transformer로 이미지 분류.
<br><br>

### [기존 문제점]
* 이미지 분류에서 Transformer는 CNN을 넘어서지 못한다.
* 특히 generalize가 잘 안된다.
<br><br>

### [해결 아이디어]
* inductive biases
    * 특화 성향. 예를들어 이미지 분류에 특화된 모델인 성향.
    * 트랜스포머는 NLP 특화 모델이다. 이미지에 대한 bias 필요.
    * inductive bias가 없는 주된 원인은 픽셀 간 상호작용을 모르는 것.
    * 인코더 내부의 MLP는 각 패치에 대한 공간 정보를 전달한다.
* hybrid architecture
    * raw image가 아닌 CNN 피쳐맵을 패치로 쪼개 넣는 구조.
    * 2가지 하이브리드(패치 임베딩 공간정보 + 각 패치 CNN 피쳐맵)
    * 이미지를 패치 피쳐맵들의 sequence로 해석하고, 트랜스포머에 넣는다.
    * CNN 장점 + 트랜스포머 장점
* self-supervised pre-training
    * GPT나 BERT들이 쓰는 사전학습 차용.
    * 레이블이 없는 대용량 데이터셋에서 복합적인 특징 추출법 학습.
    * 실전에서 이 방법을 활용하도록 유도함. (실전도 레이블이 없으니까 효과적)
<br><br>

### [추가로 볼 레퍼런스]
* Detection Transformer(End-to-end object detection with transformers)
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* BERT의 self-supervised pre-training
    * 레이블이 없는 대규모 텍스트 데이터를 이용해 사전 학습.
    * 특징을 추출하는 방법만 미리 익히는 것.
    * Masked Language Modeling, Next Sentence Prediction 2가지
    * Masked LM : 입력 문장의 단어 중 일부를 랜덤하게 마스킹 후 누락된 단어 맞추게 하기.
    * NSP : 2문장 제시 후 두번째 문장이 첫번째 문장의 다음 문장인지 여부를 예측.
    * 사전 훈련이 완료되면 fine tunning 진행.
* 멀티헤드 어텐션으로 픽셀에서도 컨볼루션 레이어를 대체할 수 있었다.
* 2x2 픽셀 사이즈로 패치하면 작은 사이즈의 이미지에서만 유용했다.
* iGPT는 이미지 해상도와 컬러 스페이스를 조절해 트렌스포머를 적용했다.
* MLP : 멀티 레이어 퍼셉트론.
* Equivariance : 입력의 변화가 출력에도 동일하게 변화를 가져오는 성질.
<br><br>