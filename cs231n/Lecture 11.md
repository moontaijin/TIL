Lecture 11. Segmentation, Localization, Detection
=================================================

**Semantic Segmentation** 이란? : 각 픽셀들에 카테고리 *label*을 할당하는것
![SemanticSegmentation](./images/seg.PNG)

픽셀을 기준으로 라벨링하기 때문에 위 사진과 같은 경우 소 두마리를 구분짓지 않고 그저 'Cow'라는 label을 붙임

# 여러가지 Semantic Segmentation Idea
## Sliding Window
입력 이미지를 여러개의 작은 이미지(patch)로 나누어 각 이미지의 중앙 pixel을 CNN을 통해 구분짓음
(이미지)
### 특징
1. 모든 픽셀에 대하여 해당 작업을 해야하기 때문에 나누는 비용이 매우 비쌈
2. 각 patch들의 겹치는 부분에 대하여 feature를 공유하지 않기때문에 비효율적인 작업이 많아짐

이러한 특징 때문에 위 방법은 사용하지 않음

## Fully Convolutional
(이미지)
H x W크기의 입력 이미지를 Conv Layer를 **이미지 크기를 보존**하며 Convolution을 진행하여 C x H x W의 결과를 출력하게함
여기서 C는 Category의 수를 의미하며 이는 모든 픽셀에 Classification score를 매겨 Classifying을 한다는 것을 알 수 있음.

### 특징
1. 모든 작업을 하나의 거대한 Conv layer스택을 이용하여 한번에 계산할 수 있음
2. training data는 모든 픽셀에 labling이 되어있어야 하기 때문에 매우 만들기 힘듬( = 전처리가 매우 힘듬)
