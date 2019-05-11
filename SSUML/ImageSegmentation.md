[이미지 세그맨테이션 Colab](https://colab.research.google.com/github/tensorflow/models/blob/master/samples/outreach/blogs/segmentation_blogpost/image_segmentation.ipynb#scrollTo=wC-byMdadAMT)의 내용을 정리한 문서이다.

# Get all the files 
[kaggle](https://www.kaggle.com/c/carvana-image-masking-challenge)에서 데이터셋을 받아오기 위한 작업이다.

데이터를 받아온 후 test데이터와 validation데이터로 분할한다(validation size=0.2)

이 데이터의 특이점은 train데이터에서 masking된 데이터가 gif로 저장되어있다는 점이다.
#

# Build our input pipeline with `tf.data`
tf.data 함수를 이용하여 **data pipeline**을 구성한다.

**data pipeline** : 한 데이터 처리 단계의 출력이 다음 단계의 입력으로 이어지는 형태로 연결된 구조

data pipeline의 진행 순서

1. label과 파일의 이름으로부터 바이트(파일형식의 데이터)를 읽어옴 (label은 자동차와 배경이 1,0으로 나누어져 저장된 데이터 = 아마 masking된 데이터?)
2. 바이트를 이미지 형식으로 디코딩함
3. 이미지를 변환시킴
4. 데이터를 섞고, 반복하고, 데이터를 일괄처리, 데이터 우선 일괄처리함(???)

이미지를 변환시키는 이유는 **data augmentation**를 위함이다.

**data augmentation** : 같은 양의 데이터를 여러 방법으로 변환(수평반전, 수직반전, 색반전 등등)하여 더 많은 데이터 셋을 만들기 위한 방법이다. 이를 통해 다양한 사진들에서도 accuracy를 증가시킬 수 있다.

4번의 경우 잘 모르겠다. batch, patch등 용어의 뜻을 정확히 이해해야할것같다.
#

# Build the model
본격적으로 모델을 생성하는 부분으로 U-Net 모델을 만든다.
U-Net은 [이전](https://github.com/moontaijin/TIL/blob/master/cs231n/Lecture%2011.md)에 작성한 글에 어느정도 정리되어 있으므로 이번에는 이 모델에서 사용된 요소들에 대해 살펴보겠다.

## conv_block
Convolution을 진행하는 함수로 padding='same', (3,3)크기의 필터를 사용하였다. 이전에 사용했던 모델은 Overfitting을 방지하기 위해 Dropout을 사용하였는데 이 모델에서는 BatchNormalization을 사용하였다.

## encoder_block
Downsampling을 진행하는 함수로 pool_size=(2,2), stride_size=(2,2)로 Maxpooling 하였다.

## decoder_block
Upsampling을 진행하는 함수로 layers.concatenate함수를 이용하여 downsampling이전의 벡터 정보로 어느정도 픽셀의 위치 정보를 복원함

# 더 공부해봐야할 것들

1. dice coefficient
2. batch, epoch, patch등 용어들
