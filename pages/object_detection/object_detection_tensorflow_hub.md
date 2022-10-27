---
title: Object Detection Tensorflow Hub
sidebar: object_detection_sidebar
summary: "Tensorflow Hub를 이용한 Object Detection 내용입니다."
permalink: object_detection_tensorflow_hub.html
folder: object_detection
---

## Object Detection Overview

Computer Vision분야에서 Deep Learning을 이용한 대표적인 시스템으로 `Detection`, `Classification`, `Segmentation`이 있습니다.
굳이 우리나라 말로 표현하자만 Detection은 탐지 혹은 검출, Classification은 분류, Segmentation은 추출 정도로
표현할 수 있을 듯 합니다.
<br><br>

간단하게 예를 들어보면
<br><br>

사진 속에 고양이가 3마리 있다고 가정해보죠.
<br><br>

> `Detection System`은 사진 속에 고양이 3마리가 있다는 것을 검출해내는 기능을 수행합니다.<br>
`Classification System`은 각각의 고양이가 어떠한 고양이인지(페르시안 고양이, 샴고양이 이런 종류)를
분류해 내는 기능을 수행하고<br>
`Segmentation System`은 사진속에서 고양이의 형체만 추출할 수 있습니다. 당연히 좌표값으로 나오겠죠.

<br>

2012년 AlexNet이 공개된 이후 `CNN`은 이미지 분류(Classification)에 있어서 당연히 사용하는 표준아닌 표준처럼
인식되었습니다. 하지만 이미지 분류에 획기적인 성과를 낸 이 CNN을 다른 응용인
`Object Detection`에 바로 적용하는데는 한계가 있었습니다.
<br><br>

왜냐하면 이미지 분류는 한개의 이미지가 있을 때 이 이미지가 무엇인지 알아내는 방법이지만
Object Detection은 이미지 내의 관심있는 객체의 위치에 물체의 위치를 알려주기 위한
`Bounding Box`를 그려야 하고 확보된 다수의 Bounding Box를 분류해줘야 하기 때문입니다.
<br><br>

{% include image.html
file='deep-learning-rcnn-classification-object-detection.png'
%}
<br>

한마디로, 더 복잡하다는 말이죠.
<br><br>

2012년 CNN이 세상에 등장한 후 Object Detection 분야에는 적용되지 못하고 있다가 2014년
`R-CNN`의 등장으로 CNN이 Object Detection 분야에 최초로 적용되었습니다.
<br><br>

아래의 그림은 R-CNN(Regions with CNN features)의 구조입니다.

{% include image.html
file='deep-learning-rcnn-architecture.png'
%}
<br>

동작순서를 간단하게 정리하면 다음과 같습니다.
<br><br>

1. 이미지를 input으로 집어 넣는다.
2. 2000개의 영역(Bounding Box)을 Selective Search 알고리즘을 통해 추출하여 잘라낸다(Cropping).
3. Cropping된 이미지를 CNN모델에 넣기 위해 정방형 사이즈(227x227 pixel size)로 resize한다.(Warping)
4. 2000개의 Warped image를 CNN 모델에 집어 넣는다.
5. 각각 Classification을 진행하여 결과를 도출한다.

<br>

R-CNN은 2-stage detector로 전체 Task를 두 가지 단계로 나누어 진행합니다.
<br><br>

* `Region Proposal`
: 물체의 위치를 찾는 일

* `Region Classification`
: 물체를 분류하는 일

<br>

R-CNN 논문에서는 이 두가지 작업을 하기 위해 총 3가지 모듈을 이용합니다.
<br><br>

* `Region Proposal`
: 카테고리와 무관하게 물체의 영역을 찾는 모듈

* `CNN`
: 각각의 영역으로부터 고정된 크기의 Feature Vector를 뽑아내는 Large Convolutional Neural Network

* `SVM`
: Classification 을 위한 선형 지도학습 모델

---

## Object Detection 예시

결국, `Object Detection`(객체 탐지)는 입력 이미지로부터 여러 개의 객체를 찾아내고
각 객체가 무엇을 나타내는지 분류하는 두 가지 작업을 처리합니다.
<br><br>

이미지에서 각 객체의 위치를 찾아내고 객체를 둘러싸는 네모박스를 그리는데 객체의 경계를
나타내는 좌표 값은 `Regression` 문제로 접근합니다. 위치를 찾아낸 각 객체가 어떤 클래스에
속하는지 `Classification`하는 문제를 그 다음에 처리하게 됩니다.
<br><br>

간단하게 그림으로 표현하면 다음 그림과 같습니다.

{% include image.html
file='deep-learning-rcnn-result-seoul-gangnam.png'
%}

---

## Tensorflow Hub

`Tensorflow Hub`(텐서플로 허브)는 이미지 분류, 객체 탐지, 스타일 전이, 텍스트 분류등
다양한 딥러닝 문제를 해결할 수 있는 `pre-trained model`을 제공하는 `repository`입니다.
<br><br>

여기서 제공하는 모델을 그대로 이용하거나 전이학습을 거쳐 개별 domain에 맞게 튜닝한 다음
사용하는게 가능합니다.
<br><br>

이번에는 이 `Tensorflow Hub`에서 제공하는 `Object Detection Model`을 사용하여
샘플 이미지로부터 객체를 추출하는 작업을 해 보도록 하겠습니다. 
<br><br>

개발환경은 `Google Colab`을 이용하도록 하겠습니다.
<br><br>

먼저 다음과 같이 Tensorflow Hub 라이브러리를 불러옵니다.

~~~python

import tensorflow as tf
import tensorflow_hub as tfhub
import matplotlib.pyplot as plt

~~~

우리는 이미 학습이 완료된 딥러닝 모델을 사용할 예정이므로 별도로 모델을 학습시키는 과정은
필요치 않습니다. 모델을 활용해 객체를 탐지하고 검출할 샘플 이미지 하나를 준비하도록 하죠.
<br><br>

아래의 사진을 이용하도록 하겠습니다.

{% include image.html
file='deep-learning-rcnn-sample-gangnam-seoul-2019.jpg'
alt='객체 탐지 예시 - 서울 강남'
%}
<br>

다음의 코드를 이용해 입력 데이터를 생성합니다.

~~~python

img_path = 'file:///content/drive/MyDrive/[Colab Home 폴더]/sample-gangnam-seoul-2019.jpg'

img = tf.keras.utils.get_file(origin=img_path)   # File 객체 생성

img = tf.io.read_file(img)  # File 객체를 string으로 변환

img = tf.image.decode_jpeg(img, channels=3) # string을 숫자 텐서로 변환

img = tf.image.convert_image_dtype(img, tf.float32) # 0 ~ 1 범위로 Normalization

print(img.shape, img)  # (1484, 2713, 3)

img_input = tf.expand_dims(img, 0) # batch_size를 추가해서 4차원 텐서로 변경
                                   # 가장 앞쪽으로 0번 축을 추가.

print(img_input.shape) # (1, 1484, 2713, 3)

~~~

데이터가 준비되었으면 `pre-trained model`을 다운받아보죠. Tensorflow Hub에는
다양한 데이터 셋으로 학습된 사전 학습 모델을 제공합니다. 다음의 링크로 접속합니다.
<br><br>

[Tensorflow Hub](https://www.tensorflow.org/hub?hl=ko){: target="_blank" }
<br><br>

접속한 후 `model > Image Problem Domains > Object Detection`을 찾아들어가면
다음과 같은 화면을 보실 수 있습니다.
<br><br>

[Tensorflow Hub - Object Detection](https://tfhub.dev/s?module-type=image-object-detection){: target="_blank" }

{% include image.html
file='deep-learning-tensorflow-hub-object-detection-webpage.png'
%}
<br>

우리가 사용할 모델은 `Open Images V4` 데이터 셋을 이용하여 `Faster R-CNN` 알고리즘으로
구현된 `inception_resnet_v2` model 입니다. 이 model은 `mobilenet_v2` model보다
속도는 느리지만 정확도가 훨씬 높은게 특징입니다.
<br><br>

`inception_resnet_v2` model의 링크를 클릭하여 링크를 복사한 후 다음의 코드를 이용하여
model을 다운로드 받습니다.

~~~python

# Tensorflow Hub에서 model load
model = tfhub.load('https://tfhub.dev/google/faster_rcnn/openimages_v4/inception_resnet_v2/1')

# model을 다운로드 후 signature(용도)를 확인합니다. 
print(model.signatures.keys())

# 해당 model은 default signature만 가지고 있습니다. 이 signature를 이용하여
# Object Detection Model instance를 생성합니다. 
object_detector = model.signatures['default']

~~~

모델이 준비되었으니 이제 입력 데이터를 이용해서 실제 Object Detection을 진행합니다.

~~~python

result = object_detector(img_input)
print(result.keys())

# detection_boxes : bounding box의 좌표(ymin, xmin, ymax, xmax)
# detection_class_entities : 검출된 class 아이디
# detection_scores : 검출 score

# 총 100개를 탐지
print(len(result['detection_scores']))  # 100

~~~

검출된 내용을 시각화해보면 될 듯 합니다. 검출된 100개의 객체 중에서 검출 score가
`0.3`보다 큰 경우만 bounding box와 예측 class를 시각화 합니다. 최대 10개의
객체만 표시되도록 설정해보죠.

~~~python

boxes = result['detection_boxes']   # 좌표값
# print(boxes.shape, boxes)  # (100, 4)

labels = result['detection_class_entities'] # 분류 예측 값
scores = result['detection_scores'] # 신뢰도(score)

# 샘플 이미지의 가로, 세로 크기
img_height, img_width = img.shape[0], img.shape[1]

# 탐지할 최대 객체의 수
obj_to_detect = 10

# 시각화
plt.figure(figsize = (15,10))

for i in range(min(obj_to_detect, boxes.shape[0])):

    if scores[i] > 0.3:
        (ymax, xmin, ymin, xmax) = (boxes[i][0] * img_height, boxes[i][1] * img_width,
                                    boxes[i][2] * img_height, boxes[i][3] * img_width)
        plt.imshow(img)
        plt.plot([xmin, xmax, xmax, xmin, xmin], [ymin, ymin, ymax, ymax, ymin],
                 color='yellow', linewidth=2)
        class_name = labels[i].numpy().decode('utf-8')
        infer_score = int(scores[i].numpy()*100)
        annotation = '{}: {}%'.format(class_name, infer_score)
        plt.text(xmin+10, ymax+20, annotation,
                 color='white', backgroundcolor='blue', fontsize=10)

~~~

시각화된 결과는 다음과 같습니다.

{% include image.html
file='deep-learning-tensorflow-hub-object-detection-result-visualization.png'
%}
<br>

End.

{% include links.html %}
