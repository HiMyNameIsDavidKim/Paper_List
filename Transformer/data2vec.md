# data2vec: A General Framework for Self-supervised Learning in Speech, Vision and Language
* BAEVSKI, Alexei, et al. Data2vec: A general framework for self-supervised learning in speech, vision and language. In: International Conference on Machine Learning. PMLR, 2022. p. 1298-1312.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* general self-supervised learning에 대한 연구이다.
* speech, NLP, CV에서 모두 동일한 러닝 메서드를 사용한 data2vec을 제안한다.
* 아이디어는 마스킹 인풋을 받아 원본 인풋의 representation을 예측하는 것이다.
* word 같은 특정 모달리티의 타겟이 아니라 컨텍스트화된 representation을 예측한다.
<br><br>

### [기존 문제점]
* SSL 연구는 각 모달리티에 집중되어 왔는데, 이건 특정 학습 편향을 유발한다.
* 이 학습 편향은 해당 모달리티에서는 도움이 되지만 다른 모달리티로 일반화할 수 없다.
* 사람은 실제로 시각 세계를 이해할 때 언어를 이해할 때와 유사한 러닝 프로세스를 거친다.
* 그런데 뉴럴 네트워크 모델들은 각 모달리티에 따로따로 별개로 학습되고 있다.
<br><br>

### [해결 아이디어]
* data2vec overview
    * 제너럴한 환경을 이해하기 위해 여러 모달리티에서 동일한 objective로 학습해보자.
    * 현재는 각 모델을 학습한 후 통합하는데, 1개의 알고리즘을 사용해 한번에 학습해보자.
    * 저자들은 각 모달리티의 masked prediction(BERT, BEiT, wave2vec)을 합쳤다.
    * latent 타겟을 학습한 뒤 멀티 헤드를 통과해 각 모달리티의 타겟으로 일반화한다.
    * Fig 1
    * Transformer를 teacher 모드, student 모드로 사용해 학습한다.
    * teacher 모드로 전체 인풋에 대한 representation을 추출하고 타겟으로 사용한다.
    * student 모드로 masked 인풋에 대한 representation을 추출한다.
    * 이를 바탕으로 전체 인풋에 대한 representation을 예측한다.
    * teacher의 웨이트는 student의 exponentially decaying average 값이다.
    * 타겟이 연속된 값이고 컨텍스트화 되어있기 때문에 기존 모델보다 representation이 풍부하다.
* Model architecture
    * 핵심은 인풋 데이터의 일부를 보고 전체 인풋 데이터를 예측하는 것이다.
    * student 모델을 학습시키며 EMA 값으로 teacher 모델을 학습한다.
    * Model architecture
        * 스탠다드 Transformer를 사용하며 각각의 모달리티에 맞는 인코딩을 한다.
        * CV는 BEiT, speech는 wave2vec, text는 BERT를 사용한다.
    * Masking
        * 인풋 샘플이 토큰 시퀀스로 임베딩된 후 일부를 마스킹 해준다.
        * 마스킹된 시퀀스를 위에서 말한 Transformer에 넣는다.
        * 마찬가지로 BEiT, wave2vec, BERT의 전략을 각각 따른다.
    * Training target
        * student 모델은 마스킹되지 않은 원본 샘플의 representation을 예측한다.
        * 입력 데이터 중에서 마스킹을 한 해당 타임 스텝의 representation만 예측한다.
    * Teacher parameterization
        * teacher 모델은 마스킹되지 않은 원본 샘플의 representation을 인코딩한다.
        * 이 파라미터는 exponentially moving average (EMA)로 업데이트 된다.
* Objective
    * 컨텍스트화된 타겟 y_t가 주어지면, Smooth L1 loss를 사용한다.
    * β는 MSE loss와 L1 loss 간에 변환을 컨트롤한다.
    * 타겟 y_t와 모델의 예측값 f(x)_t 간의 갭에 따라 결정된다.
    * 갭이 β보다 작으면 MSE, 크면 L1 으로 계산된다.
    * Smooth L1의 장점은 이상값(outliers)에 대하여 민감하게 반응하지 않는다.
<br><br>

### [결과 분석]
* Results
    * Computer vision
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


