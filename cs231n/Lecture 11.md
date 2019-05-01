Lecture 11. Segmentation, Localization, Detection
=================================================

**Semantic Segmentation** 이란? : 각 픽셀들에 카테고리 *label*을 할당하는것
![SemanticSegmentation](./images/seg.PNG)

픽셀을 기준으로 라벨링하기 때문에 위 사진과 같은 경우 소 두마리를 구분짓지 않고 그저 'Cow'라는 label을 붙임
* * *
# 여러가지 Semantic Segmentation Idea
## Sliding Window
(이미지)

입력 이미지를 여러개의 작은 이미지(patch)로 나누어 각 이미지의 중앙 pixel을 CNN을 통해 구분짓음
#### 특징
1. 모든 픽셀에 대하여 해당 작업을 해야하기 때문에 나누는 비용이 매우 비쌈
2. 각 patch들의 겹치는 부분에 대하여 feature를 공유하지 않기때문에 비효율적인 작업이 많아짐

## Fully Convolutional
(이미지)

H x W크기의 입력 이미지를 Conv Layer를 **이미지 크기를 보존**하며 Convolution을 진행하여 C x H x W의 결과를 출력하게함
여기서 C는 Category의 수를 의미하며 이는 모든 픽셀에 Classification score를 매겨 Classifying을 한다는 것을 알 수 있음.

#### 특징
1. 모든 작업을 하나의 거대한 Conv layer스택을 이용하여 한번에 계산할 수 있음
2. training data는 모든 픽셀에 labling이 되어있어야 하기 때문에 매우 만들기 힘듬( = 전처리가 매우 힘듬)
3. Convolution 과정에서 모든 입력 이미지의 크기가 유지되어야 하기 때문에 계산 비용이 매우 비쌈

(이미지)
이러한 특징때문에 일반적으로 **downsampling**과 **upsampling**을 통해 이미지의 feature맵의 크기를 조정하여 Convolution을 진행함
downsampling의 경우 strided convolution과 pooling layer를 이용하여 진행하면 된다는 것을 이전에 학습하였다.
그렇기 때문에 이번에는 upsampling에 대하여 정리해보았다.

* #### upsampling
  * Unpooling
  
  (이미지)
  
  pooling layer를 통하여 줄어든 이미지를 다시 늘리는 기법으로 강의에서는 총 3가지 방법이 나온다.
  
  **Nearest Neighbor** : 행렬의 요소를 모두 늘리기 이전에 있던 값으로 채움
  
  **Bed of Nails** : 행렬의 요소의 특정 한 부분들에만 이전에 있던 값을 채운 후 나머지에는 0으로 채움
  
  (이미지)
  
  **Max Unpooling** : Max Pooling을 진행할 때 요소의 위치를 기억해두었다가 다시 크기를 늘린 후 이전에 값이 있던 자리에 값을 넣고 나머지는 0으로 채움. 이는 Segmentation을 할 경우 픽셀의 경계를 보다 세밀하게 조정할 수 있어 좋은 방법임.
  
  * Transpose Convolution
  
  strided convolution을 진행한 데이터를 늘리기 위한 기법으로 strided convolution을 역으로 진행한다고 생각하면 편하다.
  
  (이미지) 
  
  위와같이 input의 값을 3 x 3 크기의 filter와 곱하여 output에 값을 복사한다. stride가 있을 경우 stride만큼 이동하여 output에 값을 놓는다. 이 때 서로 겹치는 부분이 있는 곳은 값들을 합쳐 계산한다.
  
  (이미지)
  
  위는 1차원 벡터에서 Transpose Convolution을 진행한것으로 과정을 조금더 편하게 확인해 볼 수 있다.
