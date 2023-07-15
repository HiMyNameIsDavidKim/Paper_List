# MLP-Mixer: An all-MLP Architecture for Vision
* TOLSTIKHIN, Ilya O., et al. Advances in neural information processing systems, 2021, 34: 24261-24272.
* NIPS 2021
<br><br>

## [`논문 요약`]

### [저자의 의도]
* convolution layer와 attention layer가 반드시 필요한건 아니다.
* 오직 MLP만 사용하는 MLP-Mixer architecture 제시.
<br><br>

### [기존 문제점]
* CNN과 ViT의 architecture가 너무 복잡하다.
<br><br>

### [해결 아이디어]
* 오직 MLP로 이루어진 architecture 구상.
    * convolution 이나 self-attention 레이어를 사용하지 말자.
    * MLP가 지역적 공간과 feature 채널에 반복적으로 작용.
* Mixing MLPs
    * Channel-mixing MLPs
        * 서로 다른 채널 간에 커뮤니케이션. (같은 위치)
        * 각 토큰(=패치)는 독립적이며, 인풋 테이블의 열 방향으로 작용한다.
        * 1x1 Conv으로 간주할 수 있다.
    * Token-mixing MLPs
        * 서로 다른 토큰 간에 커뮤니케이션. (다른 위치)
        * 각 채널(=픽셀)은 독립적이며, 인풋 테이블의 행 방향으로 작용한다.
        * single-channel depth-wise Conv으로 간주할 수 있다.
* Architecture
    * Channel-mixing -> Norm -> Token-mixing -> GAP -> FC
* 모델 별 아키텍쳐 특징 분석
    * Mixer의 아키텍쳐는 두 feature가 명확하게 나눠져 있다.
    * Features at a given spatial location (i)
        * 지역 내부의 feature를 다루는 레이어는 뭘까
        * CNN : 1x1 Conv 메인, large size kernel Conv 서포트
        * ViT : MLP 메인, self-attention 서포트
        * Mixer : channel-mixing MLP 단독
    * Features between different spatial locations (ii)
        * 서로 다른 지역의 feature를 다루는 레이어는 뭘까
        * CNN : large size kernel Conv 단독
        * ViT : self-attention 단독
        * Mixer : token-mixing MLP 단독
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


