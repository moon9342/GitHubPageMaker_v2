---
title: Object Detection Tensorflow API
sidebar: object_detection_sidebar
summary: "Tensorflow API를 이용한 Object Detection 내용입니다."
permalink: object_detection_tensorflow_api.html
folder: object_detection
---

## Tensorflow Object Detection API

Object Detection에 대한 기본적인 이해가 되었으면 이번에는 Tensorflow에서 제공하는
Object Detection API에 대해서 알아보겠습니다.
<br><br>

조금 복잡하지만 환경설정부터 천천히 알아보도록 하죠.

---

## 환경설정

이번에는 Tensorflow에서 제공하는 Object Detection API를 사용하는 방법에 대해 알아보겠습니다.
<br><br>

아래의 내용은 다음의 링크의 내용을 참조했습니다.
<br><br>

[Object Detection API with TensorFlow 2](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2.md){: target="_blank" }
<br><br>

{% include callout.html
type="danger"
content="**기본적으로 Windows platform을 기준으로 알아보도록 하겠습니다. AWS EC2 역시 비슷한 방식으로 진행하시면 됩니다.
단, 환경에 따라 혹은 사용하는 모듈의 버전에 따라 상이한 부분이 있을 수 있습니다.**"
%}
<br>

먼저 `Anaconda` 가상환경을 하나 생성한 후 필요한 module을 설치합니다.
<br><br>

tensorflow를 설치할때는 `conda install tensorflow` 하면 GPU를 인식하지 못하는 경우가
발생합니다. 이 경우 `conda install tensorflow-gpu`로 설치해야 합니다.
<br><br>

> `conda create -n tf2_od python=3.8 openssl`

> `conda activate tf2_od`

> `conda install matplotlib pillow`

> `conda install -c anaconda tensorflow-gpu`

> `conda install -c conda-forge opencv==4.5.5`

> `conda install nb_conda`

<br>

우리가 직접 Object Detection API를 이용해서 model을 구현할 것은 아닙니다.
`Tensorflow Model Garden`에서 제공하는 model을 가져와서 사용할 계획인데
`Tensorflow Model Garden`은 Tensorflow 사용자에게 최신 model에 대한
코드와 예제 그리고 Tensorflow에서 재 사용가능한 modeling library를 제공하는
Repository 입니다.
<br><br>

`Model Garden for TensorFlow`의 링크는 다음과 같습니다.
<br><br>

[Model Garden for TensorFlow](https://github.com/tensorflow/models){: target="_blank" }
<br><br>

{% include callout.html
type="danger"
content="**앞으로의 모든 명령은 Anaconda 가상환경에서 진행합니다. 즉, conda activate 명령을
이용해서 가상환경으로 진입한 후 명령을 실행시키셔야 합니다.**"
%}
<br>

가상환경이 만들어졌으면 `Jupyter Notebook Home` 폴더 밑에 `TF2_Object_Detection_API`라는 이름의 폴더를 하나 생성한 후
그 안에서 다음의 명령을 이용하여 `Model Garden for TensorFlow` repository를 clone합니다.
<br><br>

우리는 Tensorflow 2.x 버전을 사용할 것이기 때문에 현재 master branch를 clone해서
사용해야 합니다. 참고로 우리가 사용하려는 `TensorFlow Object Detection API`는
`Tensorflow 2.2`, `Python 3.6`에서 동작합니다.
<br><br>

> `git clone https://github.com/tensorflow/models.git`

<br>

그 다음에 할 일은 Google의 Protocol Buffer를 다운받아서
우리가 가지고 있는 `.proto` 파일을 컴파일하는 것입니다. `Google Colab`은 이미
설치가 되어 있습니다. `Windows`나 `AWS EC2`를 사용하는 경우 아래의 절차를 거쳐 설치해야 합니다.
<br><br>

`Protocol Buffer v3.3`은 아래의 링크에서 다운받을 수 있습니다.
<br><br>

[Protocol Buffers v3.3.0](https://github.com/protocolbuffers/protobuf/releases/tag/v3.3.0){: target="_blank" }
<br><br>

`TF2_Object_Detection_API` 폴더 아래에 `protoc-3.3`이란 폴더를 만들고 우리 환경에
맞는 파일을 그 안에 다운로드 합니다. (사실 이 파일은 어느곳에 받아도 상관없습니다. 관리하기
편하게 하기 위해 `TF2_Object_Detection_API` 폴더 안에 저장했을 뿐입니다. )
<br><br>

`Windows` 시스템일 경우 `protoc-3.3.0-win32.zip`을 다운로드 하고 `AWS EC2`인 경우 `protoc-3.3.0-linux-x86_64.zip`을
다운로드합니다.
<br><br>

다음의 링크에서도 다운로드 받을 수 있습니다. 
<br><br>
[protoc-3.3.0-win32.zip](https://drive.google.com/file/d/1QYlkx8cTtY-O8yweV_nzmohz3tcQAO6X/view?usp=sharing){: target="_blank" }
<br><br>

[protoc-3.3.0-linux-x86_64.zip](https://drive.google.com/file/d/1V2OsbBT3sj6BM1t1r7WunlM7cj0VT70i/view?usp=sharing){: target="_blank" }
<br><br>

압축을 풀면 `protoc`라는 컴파일러를 얻을 수 있고 이를 이용해 `.proto` 파일을 컴파일 합니다. 
편하게 사용하기 위해 `protoc`를 path에 잡아주시는게 좋습니다.
<br><br>

여기서 한가지 주의해야 하는데 `.proto` 파일을 살펴보면
`object_detection.protos`으로 package가 잡혀있는걸 볼 수 있습니다.
<br><br>

그래서 컴파일 할 때 다음과 같이 compile해야 오류가 나지 않습니다.
(여기서 protoc는 path가 설정되어 있고 현재 working directory는 `models/research`
입니다.)
<br><br>

다음의 명령을 이용하여 `models/research`로 이동합니다.
<br><br>

> `cd models/research`

> `protoc object_detection/protos/*.proto --python_out=.`

<br>

정상적으로 실행되면 `object_detection/protos` 폴더안에 컴파일된 `xxx_pb2.py` 파일들이
생성되는것을 보실 수 있습니다.
<br><br>

참고로 `Colab`인 경우 `Protocol Buffer`가 이미 설치되어 있기 때문에 다음의 명령어만
실행하시면 됩니다.

~~~python

!protoc object_detection/protos/*.proto --python_out=.

~~~

여기까지 잘 진행됬으면 그 다음은 TensorFlow Object Detection API를 Install 합니다.
(현재 working directory는 `models/research` 입니다.)
<br><br>

Windows인 경우 `object_detection/packages/tf2/setup.py` 파일을 `models/research`에 복사합니다. 
AWS EC2인 경우 아래의 명령을 이용해서 파일을 복사합니다.
<br><br>

> `cp object_detection/packages/tf2/setup.py .`

> `python -m pip install --use-feature=2020-resolver .`

<br>

만약 Anaconda 환경이 아니라면 중간에 오류가 발생할 수 있습니다. Anaconda의 가상환경에서 
실행시키셔야 합니다. 조금 기다리면 정상적으로 설치되는 것을 확인할 수 있습니다.
<br><br>

{% include image.html
file='deep-learning-object_detection-installation.png'
%}
<br>

설치가 성공적으로 되었는지 Test를 진행해보죠. 다음의 명령을 이용합니다.
<br><br>

> `python object_detection/builders/model_builder_tf2_test.py`

<br>

나중을 위해 `TF2_Object_Detection_API` 폴더 안에 `pretrained_model`이라는
폴더를 하나 만들어 놓도록 하죠.
<br><br>

그러면 현재까지 폴더 형태는 `TF2_Object_Detection_API` 폴더 하위에
아래 그림처럼 보이게 됩니다.

{% include image.html
file='deep-learning-object_detection-folder-structure-1.png'
%}
<br>

`Google Colab`의 경우 `AWS EC2`에서 설치, 실행한 내용을 이용해서 똑같이 처리하시면 됩니다.

---

## TFRecord Data Set 준비

환경 구축이 됬으면 이제 `Data Set`을 준비해야 합니다. Tensorflow Object Detection API는
`TFRecord file format`을 사용하는 데이터를 읽어들입니다. `TFRecord`에 대한 내용은 이전에
Deep Learning 쪽에서 한번 진행했으니 여기서는 sample로 제공되는 데이터셋을 이용해 진행하도록 하겠습니다.
<br><br>

두개의 sample이 제공되는데 `PASCAL VOC(Visual Object Classes) dataset`과
`Oxford-IIIT Pet dataset` 입니다.
<br><br>

아래의 두 링크는 제공되는 데이터셋에 대한 홈페이지 입니다. 참고로 보시면 됩니다.
<br><br>

[The PASCAL Visual Object Classes Homepage](http://host.robots.ox.ac.uk/pascal/VOC/){: target="_blank" }
<br><br>

[The Oxford-IIIT Pet Dataset](https://www.robots.ox.ac.uk/~vgg/data/pets/){: target="_blank" }
<br><br>

제공된 스크립트를 이용하여 sample의 TFRecord를 생성해서 사용하도록 하겠습니다.
(현재 working directory는 `TF2_Object_Detection_API/models/research/` 입니다.)
<br><br>

만약 Windows 시스템을 사용하고 있는 상황이라면 `wget` 명령을 사용할 수 없습니다. 다음의 링크를 이용해서
`Windows 10`에서 사용할 수 있는 `GNU Wget binary`를 다운받아서 사용하시면 됩니다.
<br><br>

[Windows binaries of GNU Wget](https://eternallybored.org/misc/wget/){: target="_blank" }
<br><br>

그런데 간혹 `Windows`에서 위의 `wget` 프로그램을 이용할 경우 파일이 깨지는 경우가 있습니다. 그런 경우 그냥 browser에서
다음의 URL을 이용해 파일을 내려받은 다음 압축을 풀어주시면 됩니다. 
<br><br>

[The PASCAL VOC Dataseta](https://drive.google.com/file/d/1-607-o96emC7wg535qSCMdNRC-iBkjcR/view?usp=sharing){: target="_blank" }
<br><br>

또한 `Windows 10` 환경에서 `Anaconda`를 사용할 때 `Unix-based command`를 사용하고 싶을 때가 많습니다.
이런경우 아래의 package를 설치해주시면 됩니다. (최적화는 글쎄요..그렇게 좋지 않은듯 보입니다.)
<br><br>

> `conda install m2-base`

~~~python

wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar

tar -xvf VOCtrainval_11-May-2012.tar

python object_detection/dataset_tools/create_pascal_tf_record.py
    --label_map_path=object_detection/data/pascal_label_map.pbtxt
    --data_dir=VOCdevkit --year=VOC2012 --set=train
    --output_path=pascal_train.record

# 한줄로 쓰면 아래와 같습니다.    
python object_detection/dataset_tools/create_pascal_tf_record.py --label_map_path=object_detection/data/pascal_label_map.pbtxt --data_dir=VOCdevkit --year=VOC2012 --set=train --output_path=pascal_train.record
    
    
python object_detection/dataset_tools/create_pascal_tf_record.py
    --label_map_path=object_detection/data/pascal_label_map.pbtxt
    --data_dir=VOCdevkit --year=VOC2012 --set=val
    --output_path=pascal_val.record

# 한줄로 쓰면 아래와 같습니다.
python object_detection/dataset_tools/create_pascal_tf_record.py --label_map_path=object_detection/data/pascal_label_map.pbtxt --data_dir=VOCdevkit --year=VOC2012 --set=val --output_path=pascal_val.record

~~~

위의 스크립트를 순차적으로 수행하면 `pascal_train.record`와 `pascal_val.record` 두 개의
TFRecord 파일을 얻을 수 있습니다. 이 두 개의 파일을 이용해 학습하고 평가를 진행해야 합니다.
(Colab인 경우 파일 I/O때문에 속도가 많이 느릴 수 있습니다.)
<br><br>

참고로 `PASCAL VOC data set`의 label에 대한 정보는
`object_detection/data/pascal_label_map.pbtxt`에서 확인할 수 있습니다.
<br><br>

이왕 하는거 `Oxford-IIIT Pet TFRecord`도 만들어 놓도록 하죠. 아래의 스크립트를
이용하여 생성하시면 됩니다.

~~~python

wget http://www.robots.ox.ac.uk/~vgg/data/pets/data/images.tar.gz

wget http://www.robots.ox.ac.uk/~vgg/data/pets/data/annotations.tar.gz

tar -xvf annotations.tar.gz

tar -xvf images.tar.gz

python object_detection/dataset_tools/create_pet_tf_record.py 
    --label_map_path=object_detection/data/pet_label_map.pbtxt 
    --data_dir=`pwd` 
    --output_dir=`pwd`

# 한줄로 쓰면 아래와 같습니다.
python object_detection/dataset_tools/create_pet_tf_record.py --label_map_path=object_detection/data/pet_label_map.pbtxt --data_dir=`pwd` --output_dir=`pwd`

~~~

위의 스크립트를 실행시키면 10개의 shard로 구성된 train, valid TFRecord 파일이 생성되게 됩니다.
<br><br>

이렇게 파일들이 생성되면 아래의 그림처럼 `Directory Structure`를 구성합니다.

{% include image.html
file='deep-learning-object_detection-directory-structure.png'
%}
<br>

일단은 `PASCAL VOC data`만 이용해서 directory 구조를 표현해보겠습니다.
<br><br>

`/TF2_Object_Detection_API` 디렉토리 아래에 `my_od_model` 디렉토리를 생성합니다.
<br><br>

그 안에 위의 그림처럼 `data` 디렉토리와 `models` 디렉토리를 생성합니다.
<br><br>

`data` 디렉토리 안에 아까 만들어놓은 `pascal_train.record`, `pascal_val.record`
두개의 파일을 복사합니다. 또한 `TF2_Object_Detection_API\models\research\object_detection\data` 폴더 안에 있는
`pascal_label_map.pbtxt` 파일도 찾아 복사합니다.
<br><br>

`models` 디렉토리 안에 `pascal_voc_model` 디렉토리를 생성합니다.
<br><br>

아래의 그림처럼 디렉토리 구조 및 파일들이 위치하면 됩니다.

{% include image.html
file='deep-learning-object_detection-directory-structure-1.png'
%}

---

## model configuration 생성

데이터셋이 준비되면 그 다음에 할 일은 `model config`를 생성하는 것입니다.
<br><br>

처음부터 config 파일을 생성하는 작업은 너무 힘이드니 기존에 있는 config 파일을 참조하여
수정해서 사용해야 합니다.
<br><br>

아래의 두 링크는 사용한 Pretrained network에 대한 config 파일들을 다운받을 수 있는
링크와 `Object Detection Training Pipeline`을 설정하는 방법에 대한 링크입니다.
<br><br>

[Pretrained network 별 config 파일](https://github.com/tensorflow/models/tree/master/research/object_detection/configs/tf2){: target="_blank" }
<br><br>

[Configuring the Object Detection Training Pipeline](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/configuring_jobs.md){: target="_blank" }
<br><br>

우리는 `faster_rcnn_resnet101_v1_800x1333_coco17_gpu-8.config`을 이용해
우리 예제(PASCAL VOC data)에 맞게 수정해서 사용하도록 하겠습니다. 처음에는
어떻게 바꿔야 할지 잘 모르니 파일경로같은 부분만 설정하도록 하겠습니다.
<br><br>

이 config 파일은 크게 5개의 부분으로 나뉘어져 있습니다.
<br><br>

1. `model`
: 학습이 진행될 모델의 종류를 설정합니다.

2. `train_config`
: SGD parameter, preprocessing, initial value와 같은 model 학습에 필요한 parameter들을 설정합니다.

3. `eval_config`
: evaluation을 수행할 때 사용될 metric을 설정합니다.

4. `train_input_config`
: 학습을 진행할 dataset을 설정합니다.

5. `eval_input_config`
: evaluation을 진행할 dataset을 설정합니다.

이 configuration 파일을 설정할 때 `checkpoint`를 이용하는게 좋습니다. 실제
Object Detection을 하기 위해 Training을 하는 경우 며칠이 걸리는게 일반적입니다. 이
학습 속도를 높이기 위해 기존에 잘 만들어놓은 `image classification` 또는
`object detection` checkpoint로 부터 추출한 `feature extractor parameters`를
재 사용합니다.
<br><br>

`train_config` 부분에 이 내용을 설정합니다.
<br><br>

> `fine_tune_checkpoint` : 존재하는 checkpoint의 prefix

> `fine_tune_checkpoint_type` : `classification` or `detection` (depending on the type)

<br>

아래의 두 링크는 `classification` 또는 `detection` checkpoint를 받을 수 있는 링크입니다.
<br><br>

[A list of classification checkpoints](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_classification_zoo.md){: target="_blank" }
<br><br>

[A list of detection checkpoints](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md){: target="_blank" }
<br><br>

이 중 우리는 detection에 관련된 checkpoint를 하나 받아 사용합니다.
<br><br>

아래의 코드는 해당 파일에 대한 경로를 알려주면 파일을 다운로드해서 압축까지 풀어주는
코드입니다. 코드를 실행시킬 때 `Jupyter Notebook Home`폴더에서 파일을 생성한 후 실행하면 됩니다.

~~~python

# Faster R-CNN ResNet101 V1 800x1333

import os
import six.moves.urllib as urllib
import tarfile

BASE_URL = 'http://download.tensorflow.org/models/object_detection/tf2/20200711/' 
MODEL_NAME = 'faster_rcnn_resnet101_v1_800x1333_coco17_gpu-8'                 
MODEL_FILE = MODEL_NAME + '.tar.gz'                         
                                                           
DOWNLOAD_URL = BASE_URL + MODEL_FILE
DOWNLOAD_PATH = './TF2_Object_Detection_API/pretrained_model'

if not os.path.isdir(DOWNLOAD_PATH):
    os.mkdir(DOWNLOAD_PATH)

opener = urllib.request.URLopener()
opener.addheader('User-Agent', 'ballentain')
opener.retrieve(DOWNLOAD_URL, filename = DOWNLOAD_PATH + '/' + MODEL_FILE)
print('모델 다운로드 완료...')

tar_file = tarfile.open(DOWNLOAD_PATH + '/' + MODEL_FILE)                                               
tar_file.extractall(DOWNLOAD_PATH)
print('압축 해제 완료...')

~~~

위의 코드를 실행시키면 `TF2_Object_Detection_API/pretrained_model` 디렉토리 안에
`faster_rcnn_resnet101_v1_800x1333_coco17_gpu-8.tar.gz` 파일이 다운로드 되고 압축이
풀립니다. 디렉토리 안에 `checkpoint`라는 디렉토리가 있는데 그 안에 `checkpoint` 파일이
들어있습니다.
<br><br>

먼저 `TF2_Object_Detection_API/my_od_model/checkpoint` 디렉토리를 생성한 후
압축을 푼 `checkpoint` 폴더안의 파일 3개를 `TF2_Object_Detection_API/my_od_model/checkpoint` 디렉토리로 복사합니다.
<br><br>

아래의 내용으로 만들어진 `my_model.config` 파일을
`TF2_Object_Detection_API/my_OD_Model/models/pascal_voc_model` 디렉토리에 저장합니다.
폴더 경로는 적절히 수정해야 합니다. 

~~~python

# Faster R-CNN with Resnet-101 (v1),
# Initialized from Imagenet classification checkpoint
#
# Train on GPU-8
#
# Achieves 36.6 mAP on COCO17 val

model {
  faster_rcnn {
    num_classes: 20
    image_resizer {
      keep_aspect_ratio_resizer {
        min_dimension: 800
        max_dimension: 1333
        pad_to_max_dimension: true
      }
    }
    feature_extractor {
      type: 'faster_rcnn_resnet101_keras'
    }
    first_stage_anchor_generator {
      grid_anchor_generator {
        scales: [0.25, 0.5, 1.0, 2.0]
        aspect_ratios: [0.5, 1.0, 2.0]
        height_stride: 16
        width_stride: 16
      }
    }
    first_stage_box_predictor_conv_hyperparams {
      op: CONV
      regularizer {
        l2_regularizer {
          weight: 0.0
        }
      }
      initializer {
        truncated_normal_initializer {
          stddev: 0.01
        }
      }
    }
    first_stage_nms_score_threshold: 0.0
    first_stage_nms_iou_threshold: 0.7
    first_stage_max_proposals: 300
    first_stage_localization_loss_weight: 2.0
    first_stage_objectness_loss_weight: 1.0
    initial_crop_size: 14
    maxpool_kernel_size: 2
    maxpool_stride: 2
    second_stage_box_predictor {
      mask_rcnn_box_predictor {
        use_dropout: false
        dropout_keep_probability: 1.0
        fc_hyperparams {
          op: FC
          regularizer {
            l2_regularizer {
              weight: 0.0
            }
          }
          initializer {
            variance_scaling_initializer {
              factor: 1.0
              uniform: true
              mode: FAN_AVG
            }
          }
        }
      }
    }
    second_stage_post_processing {
      batch_non_max_suppression {
        score_threshold: 0.0
        iou_threshold: 0.6
        max_detections_per_class: 100
        max_total_detections: 100
      }
      score_converter: SOFTMAX
    }
    second_stage_localization_loss_weight: 2.0
    second_stage_classification_loss_weight: 1.0
  }
}

train_config: {
  # 현재 AWS 성능상 batch_size를 4로 설정해도 OOM 오류가 발생.
  batch_size: 4
  # 현재 AWS 성능상 사용으로 학습에 걸리는 시간이 1step당 0.6초가 걸림
  # 200,000 step이면 약 33.3 시간이 걸리게 되니 step의 수를
  # 조절할 필요가 있음.
  num_steps: 200000
  optimizer {
    momentum_optimizer: {
      learning_rate: {
        cosine_decay_learning_rate {
          learning_rate_base: 0.01
          total_steps: 200000
          warmup_learning_rate: 0.0
          warmup_steps: 5000
        }
      }
      momentum_optimizer_value: 0.9
    }
    use_moving_average: false
  }
  gradient_clipping_by_norm: 10.0
  fine_tune_checkpoint_version: V2
  fine_tune_checkpoint: "D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/checkpoint/ckpt-0"
  fine_tune_checkpoint_type: "detection"
  from_detection_checkpoint: true
  data_augmentation_options {
    random_horizontal_flip {
    }
  }

  data_augmentation_options {
    random_adjust_hue {
    }
  }

  data_augmentation_options {
    random_adjust_contrast {
    }
  }

  data_augmentation_options {
    random_adjust_saturation {
    }
  }

  data_augmentation_options {
     random_square_crop_by_scale {
      scale_min: 0.6
      scale_max: 1.3
    }
  }
}

train_input_reader: {
  label_map_path: "D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/data/pascal_label_map.pbtxt"
  tf_record_input_reader {
    input_path: "D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/data/pascal_train.record"
  }
}

eval_config: {
  metrics_set: "coco_detection_metrics"
  use_moving_averages: false
  batch_size: 1;
}

eval_input_reader: {
  label_map_path: "D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/data/pascal_label_map.pbtxt"
  shuffle: false
  num_epochs: 1
  tf_record_input_reader {
    input_path: "D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/data/pascal_val.record"
  }
}

~~~

---

## Training & Evaluation

준비가 다 되었다면 이제 학습을 진행해보도록 하겠습니다.
<br><br>

진행하기에 앞서 오류가 발생할 수 있기때문에 다음과 같이 실행합니다.

> `pip install "opencv-python-headless<4.3"`

아래의 명령을 이용하여 학습을 진행합니다.
(현재 working directory는 TF2_Object_Detection_API/models/research 입니다.)
<br><br>

`Windows 10` 시스템에서는 환경변수를 등록해야 합니다. 제어판에서 등록하는 방법도 있지만 간단히 `cmd` 창에서 등록하는 방법을
이용하는게 좋을 듯 합니다. 환경변수 등록은 아래와 같은 방식으로 하면 됩니다. 

~~~python

# Windows 10 cmd 창에서 환경변수 등록

# echo 명령어를 사용하면 %%를 이용하여 환경변수 내용을 알아낼수 있습니다.
> echo %PATH%

# path 환경변수에 새로운 경로를 추가하는 경우(일시적)
> set path =%path%;추가하고자하는경로;
> set path=%path%;c:/java/bin;

# path 환경변수에 새로운 경로를 추가하는 경우(영구적)
> setx path "%PATH%;추가하고자하는경로"
> setx path "%PATH%;c:/java/bin"
 
~~~

~~~python

# PIPELINE_CONFIG_PATH={path to pipeline config file}
# for Windows 10, 
set PIPELINE_CONFIG_PATH=D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/my_model.config
# PIPELINE_CONFIG_PATH=/home/tutor/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/my_model.config

# MODEL_DIR={path to model directory}
# ${MODEL_DIR} points to the directory in which training checkpoints 
# and events will be written.
# for Windows 10, 
set MODEL_DIR=D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/
# MODEL_DIR=/home/tutor/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/


set XLA_FLAGS=--xla_gpu_cuda_data_dir="C:/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v11.8"


# for Windows 10, 
python object_detection/model_main_tf2.py --pipeline_config_path=%PIPELINE_CONFIG_PATH% --model_dir=%MODEL_DIR% --alsologtostderr

# python object_detection/model_main_tf2.py \
#     --pipeline_config_path=${PIPELINE_CONFIG_PATH} \
#     --model_dir=${MODEL_DIR} \
#     --alsologtostderr

~~~

정상적으로 수행되면 아래와 같은 메시지가 출력되면서 학습이 진행됩니다.

~~~python

INFO:tensorflow:Step 300 per-step time 0.619s
I1026 11:18:41.157780 139735268599616 model_lib_v2.py:698] Step 300 per-step time 0.619s
INFO:tensorflow:{'Loss/BoxClassifierLoss/classification_loss': 0.23231453,
 'Loss/BoxClassifierLoss/localization_loss': 0.42732975,
 'Loss/RPNLoss/localization_loss': 0.0930305,
 'Loss/RPNLoss/objectness_loss': 0.103011966,
 'Loss/regularization_loss': 0.0,
 'Loss/total_loss': 0.8556867,
 'learning_rate': 0.00059999997}
I1026 11:18:41.158109 139735268599616 model_lib_v2.py:701] {'Loss/BoxClassifierLoss/classification_loss': 0.23231453,
 'Loss/BoxClassifierLoss/localization_loss': 0.42732975,
 'Loss/RPNLoss/localization_loss': 0.0930305,
 'Loss/RPNLoss/objectness_loss': 0.103011966,
 'Loss/regularization_loss': 0.0,
 'Loss/total_loss': 0.8556867,
 'learning_rate': 0.00059999997}

~~~

학습이 종료되면 다음의 명령을 이용해서 Evaluation을 진행합니다.

~~~python

PIPELINE_CONFIG_PATH=D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/my_model.config

MODEL_DIR=D:/Jupyter_Home/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/

CHECKPOINT_DIR=${MODEL_DIR}

python object_detection/model_main_tf2.py 
    --pipeline_config_path=${PIPELINE_CONFIG_PATH} 
    --model_dir=${MODEL_DIR} 
    --checkpoint_dir=${CHECKPOINT_DIR} 
    --alsologtostderr

# 한줄로 쓰면 아래와 같습니다.
python object_detection/model_main_tf2.py --pipeline_config_path=${PIPELINE_CONFIG_PATH} --model_dir=${MODEL_DIR} --checkpoint_dir=${CHECKPOINT_DIR} --alsologtostderr

~~~

아래의 링크를 클릭하시면 `Google Colab`에서 실행한 `ipynb`파일을
받으실 수 있습니다. Colab을 이용할 경우 위에서 설명한 내용외에 환경설정부분에서
약간 다른점이 있다는 것 염두해 두세요!!
<br><br>

[Tensorflow Object Detection API train example for Google Colab](https://drive.google.com/file/d/18l2k-ieZF_XrK1-V7ChMHkCiH1gzn1NP/view?usp=sharing){: target="_blank" }

---

## Prediction

우리의 모델이 완성되었으니 이제 Prediction을 해 보겠습니다.
<br><br>

`Jupyter Notebook`에서 실행시키시면 됩니다.
<br><br>

코드는 다음의 링크를 참조했습니다.
<br><br>

[Intro to Object Detection Colab](https://github.com/tensorflow/models/blob/master/research/object_detection/colab_tutorials/inference_tf2_colab.ipynb){: target="_blank" }

~~~python

import os
import io
import scipy.misc
import numpy as np
from six import BytesIO
from PIL import Image, ImageDraw, ImageFont
from IPython.display import Image as ipython_image

import tensorflow as tf

from object_detection.utils import label_map_util
from object_detection.utils import config_util
from object_detection.utils import visualization_utils as viz_utils
from object_detection.builders import model_builder

~~~

~~~python

def load_image_into_numpy_array(path):
  # Load an image from file into a numpy array.

  # Args:   
  #  path: the file path to the image

  # Returns:
  #  uint8 numpy array with shape (img_height, img_width, 3)
    img_data = tf.io.gfile.GFile(path, 'rb').read()
    image = Image.open(BytesIO(img_data))
    (im_width, im_height) = image.size
    return np.array(image.getdata()).reshape((im_height, im_width, 3)).astype(np.uint8)


def get_keypoint_tuples(eval_config):
  # Return a tuple list of keypoint edges from the eval config.
  
  # Args:
  #  eval_config: an eval config containing the keypoint edges
  
  # Returns:
  #  a list of edge tuples, each in the format (start, end)
    tuple_list = []
    kp_list = eval_config.keypoint_edge
    for edge in kp_list:
        tuple_list.append((edge.start, edge.end))
    return tuple_list

~~~

~~~python

!export CUDA_VISIBLE_DEVICES=0

pipeline_config = '/home/tutor/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model/my_model.config'
model_dir = '/home/tutor/TF2_Object_Detection_API/my_od_model/models/pascal_voc_model'

# Load pipeline config and build a detection model
configs = config_util.get_configs_from_pipeline_file(pipeline_config)
model_config = configs['model']
detection_model = model_builder.build(
      model_config=model_config, is_training=False)

# # Restore checkpoint
ckpt = tf.compat.v2.train.Checkpoint(model=detection_model)
# ckpt-1의 값은 수정.
ckpt.restore(os.path.join(model_dir, 'ckpt-121')).expect_partial()

def get_model_detection_function(model):
  # Get a tf.function for detection.

    @tf.function
    def detect_fn(image):
    # Detect objects in image.
        image, shapes = model.preprocess(image)
        prediction_dict = model.predict(image, shapes)
        detections = model.postprocess(prediction_dict, shapes)
        
        return detections, prediction_dict, tf.reshape(shapes, [-1])
    
    return detect_fn

detect_fn = get_model_detection_function(detection_model)

~~~

~~~python

label_map_path = configs['eval_input_config'].label_map_path
label_map = label_map_util.load_labelmap(label_map_path)
categories = label_map_util.convert_label_map_to_categories(
    label_map,
    max_num_classes=label_map_util.get_max_label_map_index(label_map),
    use_display_name=True)
category_index = label_map_util.create_category_index(categories)
label_map_dict = label_map_util.get_label_map_dict(label_map, use_display_name=True)

~~~

~~~python

# image_dir = 'models/research/object_detection/test_images/'
# image_path = os.path.join(image_dir, 'image2.jpg')
image_dir = '/home/tutor/TF2_Object_Detection_API/models/research/voc_train/VOCdevkit/VOC2012/JPEGImages/'
image_path = os.path.join(image_dir, '2008_008021.jpg')
image_np = load_image_into_numpy_array(image_path)

# Things to try:
# Flip horizontally
# image_np = np.fliplr(image_np).copy()

# Convert image to grayscale
# image_np = np.tile(
#     np.mean(image_np, 2, keepdims=True), (1, 1, 3)).astype(np.uint8)

input_tensor = tf.convert_to_tensor(
    np.expand_dims(image_np, 0), dtype=tf.float32)
detections, predictions_dict, shapes = detect_fn(input_tensor)

label_id_offset = 1
image_np_with_detections = image_np.copy()

# # Use keypoints if available in detections
keypoints, keypoint_scores = None, None
if 'detection_keypoints' in detections:
    keypoints = detections['detection_keypoints'][0].numpy()
    keypoint_scores = detections['detection_keypoint_scores'][0].numpy()

# print(detections['detection_boxes'][0].numpy())
# print(detections['detection_classes'][0].numpy())
# print(detections['detection_scores'][0].numpy())

viz_utils.visualize_boxes_and_labels_on_image_array(
      image_np_with_detections,
      detections['detection_boxes'][0].numpy(),
      (detections['detection_classes'][0].numpy() + label_id_offset).astype(int),
      detections['detection_scores'][0].numpy(),
      category_index,
      use_normalized_coordinates=True,
      max_boxes_to_draw=200,
      # min_score_thresh를 적절히 설정
      min_score_thresh=.70,
      agnostic_mode=False,
      keypoints=keypoints,
      keypoint_scores=keypoint_scores,
      keypoint_edges=get_keypoint_tuples(configs['eval_config'])
      )

# print(type(image_np_with_detections), image_np_with_detections.shape)
tmp = Image.fromarray(image_np_with_detections)
tmp.save('./test_result.jpg')
ipython_image('./test_result.jpg')
# plt.figure(figsize=(12,16))
# plt.imshow(image_np_with_detections)
# plt.show()

~~~

아래는 결과 이미지입니다. 학습시간이 너무 오래 걸려서(33시간) 중간에 멈춰서
checkpoint를 이용해서 prediction 해 본 결과입니다.

{% include image.html
file='deep-learning-object_detection_result.png'
%}
<br>

End.

{% include links.html %}
