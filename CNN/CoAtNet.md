# CoAtNet: Marrying Convolution and Attention for All Data Sizes
* DAI, Zihang, et al. Coatnet: Marrying convolution and attention for all data sizes. Advances in Neural Information Processing Systems, 2021, 34: 3965-3977.
* NIPS 2021.
<br><br>

## [`논문 요약`]

### [저자의 의도]
* 
<br><br>

### [기존 문제점]
* 
<br><br>

### [해결 아이디어]
* 
<br><br>

### [추가로 볼 레퍼런스]
* MBConv Blocks
* Mobilenetv2: Inverted residuals and linear bottlenecks
<br><br>

### [내 아이디어]
* 
<br><br>



## [`메모`]

### [배경 지식]
* Depthwise Separable Convolution
    * 기존 Conv보다 빠르다.
    * 채널 방향의 연산을 하지 않기 때문.
    * (3D -> 1D) vs (2D -> 1D, 2D -> 1D) 연산량 약 1/9 감소.
* Inverted Residual Connection
    * Residual: wide -> narrow -> wide (wide layers are connected)
    * Inverted residual: narrow -> wide -> narrow (narrow layers are connected)
    * 압축된 양질의 정보가 저차원에서도 저장되어 있다는 가정.
* Relative attention
    * 
<br><br>


