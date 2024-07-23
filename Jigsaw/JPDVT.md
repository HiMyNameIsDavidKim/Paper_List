# Solving Masked Jigsaw Puzzles with Diffusion Vision Transformers
* LIU, Jinyang, et al. Solving Masked Jigsaw Puzzles with Diffusion Vision Transformers. In: Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024. p. 23009-23018.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 이미지와 비디오 직소 퍼즐 task는 어려운 과제이다.
* 기존 방법들은 효과적으로 직소 퍼즐을 푸는데 한계가 있고, DiT를 활용한 JPDVT를 제안한다.
* 추가로, 두 퍼즐 task를 함께 풀 수 있는 통합 프레임워크를 만들고자 했다.
<br><br>

### [기존 문제점]
* 기존 방법들은 discriminative (특히 classification) 모델 이다.
* 기존 방법의 핵심은 백본이 고도의 구별 가능한 위치 feature를 추출하게 하는 것이다.
* 하지만 이 접근 방식은 많은 수의 경우를 다룰 때 어려움을 겪는다.
* 또한, 학습 순서를 우선시하기 때문에 missing 조각이 있는 직소 퍼즐을 푸는데 적합하지 않다.
<br><br>

### [해결 아이디어]
* JPDVT overview
    * Fig 1
    * Jigsaw Puzzles with Diffusion Vision Transformers
    * condition diffusion denoising probabilistic model(CDDPM)을 활용했다.
    * 전체 퍼즐 조각의 위치를 학습하고 missing 조각을 generate 하도록 설계되었다.
    * 모델은 사용 가능한 조각의 visual context를 활용한다.
    * 이미지 데이터셋 뿐만 아니라 비디오 데이터셋도 사용한다.
    * 이미지 퍼즐과 시간 퍼즐을 모두 풀 수 있는 통합 디자인이다.
* Our approach
    * Masked jigsaw transformer diffusion
        * Fig 3
        * 인풋 이미지 or 비디오를 X라고 하자.
        * 섞인 퍼즐 조각 하나하나를 x_1, ..., x_N이라 하자.
        * 모델의 objective는 함수 f를 사용해 X를 올바른 위치 l으로 재구성하는 것이다.
        * 각 패치의 visual embedding을 e라고 하자.
        * 위치 l의 position embeding을 r(l)라고 하자.
        * Fig 2
        * 포지션 임베딩은 Fig 2 그림처럼 점점 노이즈가 생긴다.
        * 저자들은 이게 MAE와 유사하다고 주장한다.
        * MAE와 다른 점은 조각이 섞였기 때문에 e와 r(l)을 별개로 처리해야한다.
        * 여기서 시나리오는 missing 조각이 있냐 없냐에 따라 2개로 나뉘어진다.
        * Puzzles without missing pieces
            * 완전한 세트 E = {e_1, ..., e_N}와 L = {r(l_1), ..., r(l_N)}의 경우다.
            * 직소 퍼즐 task만 푸는 문제다.
            * forward에서는 CDDPM으로 L에 노이즈를 주며 diffusion 한다.
            * 노이즈를 주는 양은 β로 조절하고, 노이즈를 주는 횟수는 t이다.
            * reverse에서는 L을 디노이즈 하며 복원한다.
            * 결론적으로 loss는 마지막 수식과 같다.
        * Puzzles with missing pieces
            * visual embedding 세트 E 중에 E_g는 given 조각, E_m은 missing 조각이다.
            * given 조각과 missing 조각 모두에서 위치 정보를 generate 한다.
            * missing 조각 인페인팅에는 기존 모델 LaMa를 사용했다. (삼성 리서치 논문. 펄럭.)
            * (위치 복원 후 인페인팅 순차 진행) or (위치 복원과 인페인팅 동시 진행) 이 있다.
            * 저자들은 동시 진행을 선택했다. (?)
    * Implementation Details
        * Image
            * 이미지 직소 퍼즐은 DiT를 사용했다.
            * 모델에 들어온 'initial 패치'의 위치 임베딩은 고정한다.
            * 노이징 디노이징을 할 때는 'initial 패치'에 대한 상대적 임베딩(=차이)을 사용한다.
            * 이 접근법은 공간 종속성을 포착하는 능력 향상과 절대적 위치의 과도한 의존을 방지한다.
            * (추가 설명이 없어서 이해 못한 부분임.)
            * diffusion step T는 1000 이다.
        * Temporal
            * 기존 방법들을 활용하여 셋업한다.
            * 높은 해상도의 비디오는 해상도를 줄인다.
            * 비디오를 시퀀스로 만들기 위해 Make-a-video 모델을 사용했다.
            * 마찬가지로 diffusion step T는 1000 이다.
<br><br>

### [결과 분석]
* Experiments
    * Image jigsaw puzzles
        * ImangeNet-1k와 JPwLEG-3에서 평가했다.
        * SOTA 모델에 대한 비교 평가를 실시했다.
        * ImangeNet-1k: 255 사이즈 이미지 -> 85 사이즈 조각 -> 64 사이즈 크롭, perm = 9!
        * JPwLEG-3: 398 사이즈 이미지 -> 96 사이즈 조각 크롭, perm = 미기재
        * Fig 4
        * missing 조각 1~3개일 경우 프로세스 예시 그림이다.
        * Table 1
        * ImageNet은 퍼즐레벨 기준 68.7%, 조각레벨 기준 83.3% 정확도를 얻었다.
        * JPwLEG-3는 조각레벨 기준 71.3% 정확도를 얻었다.
    * Temporal jigsaw puzzles
        * (생략)
<br><br>

### [추가로 볼 레퍼런스]
* 
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]