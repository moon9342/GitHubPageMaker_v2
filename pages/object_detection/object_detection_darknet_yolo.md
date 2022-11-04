---
title: Object Detection Darknet YOLO
sidebar: object_detection_sidebar
summary: "Darknet YOLO를 이용한 Object Detection 내용입니다."
permalink: object_detection_darknet_yolo.html
folder: object_detection
---

## 개발 환경

`Windows Anaconda`를 target으로 합니다. `C:/YOLO_Object_Detection` 폴더를 생성한 후 그 안에서 작업합니다.
<br><br>

`Windows 10`에 `darknet`을 설치하는 방법은 `vcpkg`를 이용하는 방법이 가장 좋습니다. 
<br><br>

`CUDA`를 설치하기 전에 `Visual Studio 2019`를 먼저 설치해야 합니다. 그렇지 않으면 설정 오류가 발생할 수 있습니다.
주의해야 할 점은 `Visual Studio 2019` 설치 시 사용언어를 `English`로 설치해야 하고 C++ 사용이 가능한 `workload`를 포함해서
설치해야 합니다. 약 8G의 설치공간이 필요합니다. `Visual Studio 2022` 버전은 CUDA compiler를 찾지 못하는 오류를 발생시킬 수 있습니다.
<br><br>

다음과 같은 프로그램을 같이 설치해야 합니다.
<br><br>

`CMake`를 사용하기 위한 [CMake](https://drive.google.com/file/d/1-36eQHrOsJrgbNfi_nBSY0TQen_spzvm/view?usp=sharing){: target="_blank" }
<br><br>

`Visual Studio`를 사용하기 위한 [Visual Studio 2019](https://drive.google.com/file/d/1-RWEBGA_jlT7PBn6cIgzyruxTe-OWycR/view?usp=sharing){: target="_blank" }
<br><br>

설치에 대한 자세한 사항은 다음의 Github를 참조하시면 됩니다.
<br><br>

[AlexeyAB/darknet Github](https://github.com/AlexeyAB/darknet){: target="_blank" }
<br><br>

`CMake`를 설치한 후 Visual Studio 2019를 설치합니다. 그 이후에 CUDA를 설치하고 여기까지 설치가 끝나면
`Windows Powershell`을 실행시켜서 다음의 명령을 수행시킵니다. 
(현재 working directory는 `C:/YOLO_Object_Detection`입니다.)
<br><br>

> `Set-ExecutionPolicy unrestricted -Scope CurrentUser -Force`

> `git clone https://github.com/AlexeyAB/darknet`

> `cd darknet`

> `.\build.ps1 -UseVCPKG -EnableOPENCV -EnableCUDA -EnableCUDNN`

---

## YOLOv4

`YOLO`는 객체 탐지(Object Detection)분야에서 많이 알려진 논문인
`You Only Look Once: Unified, Real-Time Object Detection (2016)`을 의미합니다.
이를 줄여서 `YOLO`라고 하는 것이고 이는 위에서 설명한 R-CNN 모델보다 더 높은 정확도를
추구하는것이 아니라 근접한 정확도를 가지고 더 많은 양의 이미지를 처리할 수 있는
`실시간 객체 탐지`를 위해서 등장했습니다.
<br><br>

`YOLO`는 2016년 처음 등장한 이후 지속적으로 논문이 출간되어 현재 다양한 버전의
`YOLO`가 존재합니다. 보통 편의상 버전명을 붙여서 `YOLOv<버전명>`으로 부르고 있습니다.
2022년10월 기준으로 `YOLOv7`까지 나와있습니다.
<br><br>

참고로 `YOLOv5`부터는 `pytorch`로 구현되어 있기 때문에 `Darknet`을 이용해서 사용할 수 없습니다. 
구현방법이 달라지기 때문에 상위버전은 `pytorch`를 이용해야 합니다. 
<br><br>

`YOLO`는 3가지 특징으로 정리할 수 있는데 논문의 제목에 사실 나와있습니다.
<br><br>

* `You Only Look Once`
: 이미지 전체를 단 한번만 모델에 통과시킵니다. R-CNN과 비교하자면 R-CNN은 이미지에서 일정한 규칙으로 이미지를 
여러장 쪼개서 CNN 모델을 통과시키기 때문에 한 장의 이미지에서 객체 탐지를 수행해도 실제로는 수 천장의 이미지를 모델에 통과시키게 됩니다.
반면, YOLO는 이미지 전체를 말 그대로 단 한번만 모델에 통과시킵니다.

* `Unified`
: 통합된 모델을 사용합니다. 다른 객체 탐지 모델 들은 다양한 전처리 모델과 인공 신경망을 결합해서 사용하지만,
YOLO는 단 하나의 인공신경망에서 이를 전부 처리합니다. 이런 특징 때문에 YOLO가 다른 모델보다 간단해 보입니다.

* `Real-time Object Detection`
: 실시간 객체 탐지. 오늘 날 YOLO를 유명하게 만든 특징입니다. YOLO가 높은 성능으로 객체를 탐지하는 모델은 아니지만, 
실시간으로 여러장의 이미지를 탐지할 수 있습니다. `빠르다`는 이름이 붙은 `Fast R-CNN`이 0.5 FPS(초당 프레임 수)의 성능을 가진 반면에
YOLO는 45 FPS의 성능을 가지고 있습니다. 이는 영상을 스트리밍 하면서 동시에 화면 상의 물체를 부드럽게 구분할 수 있을 정도입니다.

<br>

우리가 사용할 것은 `Darknet`이라는 Framework입니다. Darknet은 `C`언어로 구현되어 있어
속도가 빠르고 `CUDA` 기반의 다양한 딥러닝 model을 제공하는 오픈 소스 프레임워크입니다.
<br><br>

`YOLO` model을 개인의 local PC 환경에서 학습하는 것은 사실상 불가능 합니다. 성능 좋은
`GPU`도 필요하고 학습시간도 상당히 오래 걸리기 때문입니다. 그래서 `Darknet`에서 제공하는
`Pre-trained model`을 활용하는 것이죠. 
<br><br>

오리지널 Darknet은 `Joseph Redmon`이 만들었는데 이보다 `YOLOv4`의 저자인 `Alexey Bochkovskiy`의
버전을 더 많이 사용합니다. (현재는 YOLOv7이 최신버전입니다.)
<br><br>

모델을 만들기 위해서는 당연히 데이터가 필요합니다. Darknet 학습을 위해서는 이미지와 label의 쌍으로 이루어진
데이터셋이 필요합니다. 만약 우리가 수집한 데이터를 이용해서 Darknet을 학습시킬려면 이 레이블링 작업을 직접해야
합니다.
<br><br>

레이블링 작업은 여러가지 방법이 있지만 `Yolo_label`이라는 툴을 이용하면 만들어진 데이터를 별도의 전처리 과정없이
바로 Darknet에 주입할 수 있어서 편리합니다.
<br><br>

Darknet을 학습시키기 앞서 기 학습된 가중치를 이용해 Darknet을 사용하는 것부터 먼저 알아보도록 하겠습니다.
<br><br>

AWS EC2를 사용할 경우 `Makefile`을 수정해야 하는데 다음과 같이 수정하시면 됩니다. GPU를 사용할 경우 설정이
잡혀 있어야 합니다. (Windows인 경우 따로 make를 진행할 필요 없습니다.)

~~~text

GPU=0
CUDNN=0
CUDNN_HALF=0
OPENCV=0
AVX=0
OPENMP=0
LIBSO=0

~~~

이렇게 되어 있는 부분을 아래와 같이 수정합니다. GPU를 사용하지 않는 경우 해당 설정은 제외합니다.

~~~text

GPU=1
CUDNN=1
CUDNN_HALF=1   # 학습시간을 향상시켜주기위한 옵션 => 1로 변경
OPENCV=0       # 1로 설정할 경우 컴파일에는 문제 없지만 Prediction시 Display 오류가 발생합니다. 
AVX=0
OPENMP=0
LIBSO=1  # 이전 redmon의 darknet은 make하면 빠져나왔던 so 파일이 옵션으로 추가. 
         # 응용 프로그램에 yolo를 사용하기 위해 1로 설정해서 so 파일까지 생성.
~~~

컴파일을 수행한 후 `weights` 파일을 다운로드 합니다.
<br><br>

> `make`

<br>

[yolov4.weights](https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.weights){: target="_blank" }를 클릭해서
다운로드 합니다. 
<br><br>

아래의 링크에서도 다운로드 가능합니다.
<br><br>

[yolov4.weights(Google Drive)](https://drive.google.com/file/d/1y2xp5PjMDEbFF4a5GC4lURbXJV8BCkBB/view?usp=sharing){: target="_blank" }
<br><br>

혹은 다음 명령을 이용하여 다운로드 합니다.
<br><br>

> `wget https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.weights`

<br>

이제 다음 명령을 실행해 결과를 확인합니다.
<br><br>

> `./darknet detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights data/dog.jpg`

<br>

만약 AWS EC2에서 `OpenCV` 옵션으로 compile한 경우 display 에러가 발생하면서 그림이 보이지
않게 되는데 이때 `Xming`을 설치해서 `putty`설정을 잡아주면 이미지를 볼 수 있습니다. 이 설정은
구글링을 통해서 처리해보세요. 일단 우리는 OpenCV를 제외하고 사용했기 때문에 결과가 이미지
파일로 저장됩니다.
<br><br>

처리된 결과는 다음과 같습니다.
<br><br>

>data/dog.jpg: Predicted in 20687.541000 milli-seconds.<br>
bicycle: 92%<br>
dog: 98%<br>
truck: 92%<br>
pottedplant: 33%<br>
Not compiled with OpenCV, saving to predictions.png instead

<br>

저장된 이미지 파일을 확인하면 다음과 같습니다.

{% include image.html
file='machine-learning-yolov4-sample-image.png'
%}
<br>

YOLOv4는 `Bounding Box`의 좌표알 수 있습니다. darknet 실행 시 option을
이용해 좌표를 얻는 방법도 있는것으로 기억하는데 이 option은 검색을 통해서 알아봐야 할 듯 합니다.
<br><br>

가장 간단한 방법은 YOLO의 source 코드를 직접 수정해서 다시 `build`해 사용하는 것입니다. 이 경우
다시 컴파일을 진행해야 합니다.
`image.c` 파일 중 아래의 코드처럼 draw_box를 그려주는 함수 부분을 찾아 좌표를 출력하는 코드를 삽입합니다.

~~~c++

// 아래의 프린트문 추가
printf("Bounding Box: Left=%d, Top=%d, Right=%d, Bottom=%d\n", left, top, right, bot); 
draw_box_width(im, left, top, right, bot, width, red, green, blue);

~~~

`stackoverflow`같은 사이트를 참고하면 다양한 형태로 Bounding Box의 좌표를 얻는 방법을 찾을 수 있습니다.

---

## YOLOv4 학습을 위한 데이터 준비

학습을 위해 데이터 셋을 2개 준비해 보도록 하겠습니다. 하나는 `COCO data set`이고
다른 하나는 `PASCAL VOC data set`입니다. 차례대로 데이터셋을 생성해 보죠.
<br><br>

데이터셋은 두 종류를 생성하지만 실제로 학습은 `PASCAL VOC data set`으로만 진행합니다. 

---

## COCO Data Set

`COCO(Common Object in Context) dataset`을 이용해서 YOLOv4을 학습시키는 방법에 대해서 알아보겠습니다.
<br><br>

`YOLO_Object_Detection` 폴더안에 `COCO dataset`을 다운로드 한 후 진행하도록 하겠습니다.
(`C:/YOLO_Object_Detection`에서 진행합니다.)
<br><br>

아래의 링크를 타고 들어가면 COCO dataset을 살펴볼 수 있습니다.
<br><br>

[COCO dataset](https://cocodataset.org/#download){: target="_blank" }
<br><br>

머신러닝을 위해 많은 데이터 셋이 만들어져 있는데 그 중에 COCO dataset은 object detection,
segmentation을 위한 데이터셋으로, 매년 다른 데이터셋으로 만들어져 전 세계의 여러 대학/기업이 참가하는
대회에 사용되고 있습니다.
<br><br>

다운로드 링크를 보면 다음과 같은 내용의 dataset이 제공되는 것을 확인할 수 있습니다.
<br><br>

* 이미지
    * 2014 Train images [83K/13GB]
    * 2014 Val images [41K/6GB]
    * 2014 Test images [41K/6GB]
    * 2015 Test images [81K/12GB]
    * 2017 Train images [118K/18GB]
    * 2017 Val images [5K/1GB]
    * 2017 Test images [41K/6GB]
    * 2017 Unlabeled images [123K/19GB]

* 주석(Annotations)
    * 2014 Train/Val annotations [241MB]
    * 2014 Testing Image info [1MB]
    * 2015 Testing Image info [2MB]
    * 2017 Train/Val annotations [241MB]
    * 2017 Stuff Train/Val annotations [1.1GB]
    * 2017 Panoptic Train/Val annotations [821MB]
    * 2017 Testing Image info [1MB]
    * 2017 Unlabeled Image info [4MB]

용량이 상당한데 이를 browser에서 다운받을 수도 있고 Linux 명령어 `curl`을 이용해서 다운로드 받을수도 있습니다.

여기서는 다음의 shell script를 이용해서 `wget` 명령을 이용해서 다운로드 하겠습니다. (시간이 약간 걸립니다.)

~~~python

mkdir coco
cd coco
mkdir images
cd images

wget -c http://images.cocodataset.org/zips/train2017.zip
wget -c http://images.cocodataset.org/zips/val2017.zip
wget -c http://images.cocodataset.org/zips/test2017.zip
wget -c http://images.cocodataset.org/zips/unlabeled2017.zip

unzip train2017.zip
unzip val2017.zip
unzip test2017.zip
unzip unlabeled2017.zip

rm train2017.zip
rm val2017.zip
rm test2017.zip
rm unlabeled2017.zip 

cd ../
wget -c http://images.cocodataset.org/annotations/annotations_trainval2017.zip
wget -c http://images.cocodataset.org/annotations/stuff_annotations_trainval2017.zip
wget -c http://images.cocodataset.org/annotations/image_info_test2017.zip
wget -c http://images.cocodataset.org/annotations/image_info_unlabeled2017.zip

unzip annotations_trainval2017.zip
unzip stuff_annotations_trainval2017.zip
unzip image_info_test2017.zip
unzip image_info_unlabeled2017.zip

rm annotations_trainval2017.zip
rm stuff_annotations_trainval2017.zip
rm image_info_test2017.zip
rm image_info_unlabeled2017.zip

~~~

`coco.sh` 파일을 생성한 후 위의 코드를 저장한다음 shell script를 실행시키면 다운로드가 실행됩니다.
Windows의 경우 Powershell에서 실행시키시면 됩니다.
<br><br>

`train2017` 이미지 데이터는 약 118K장으로 구성되어 있으며 annotation은 json 파일로 되어 있으며
다음과 같이 구성되어 있습니다.
<br><br>

* caption : 영상에 대한 설명
* person_keypoints : 사람의 관절좌표 데이터
* instances : 영상에 포함된 사람 혹은 사물에 대한 category와 segmentation 정보

<br>

참고로 `JSON` 파일을 편하게 보기 위해서는 다음과 같은 online viewer가 필요합니다.
(파일의 크기가 작을 경우 편하게 사용할 수 있지만 파일의 크기가 커지면 오류가 발생합니다. 이런 경우
윈도우에서 `Notepad++`같은 프로그램을 이용하시는게 더 좋습니다.)
<br><br>

COCO 데이터 주석 확인은 아래와 같이 json viewer 를 통해 볼 수 있습니다. 단지 보기 너무 불편하기 때문에
Chrome의 extension 프로그램을 이용하던지 아니면 아래의 명령을 이용해서 json 내용을 줄바꿈한
파일을 생성해서 JSON Viewer를 이용하시면 됩니다.
<br><br>

[Online JSON Viewer](http://jsonviewer.stack.hu/){: target="_blank" }
<br><br>

> `jq . instances_train2017.json > instances_train2017_jq.json`

<br>

`instance_train2017_jq.json` 파일의 내용을 확인하면 다음과 같은 형식으로 내용이 나오게 됩니다.
<br><br>

~~~javascript

{ 
  "info": { 
    "description": "COCO 2017 Dataset", 
    "url": "http://cocodataset.org", 
    "version": "1.0", 
    "year": 2017, 
    "contributor": "COCO Consortium", 
    "date_created": "2017/09/01" 
  }, 
  "licenses": [ 
    { 
      "url": "http://creativecommons.org/licenses/by-nc-sa/2.0/", 
      "id": 1, 
      "name": "Attribution-NonCommercial-ShareAlike License" 
    }, 
    { 
      "url": "http://creativecommons.org/licenses/by-nc/2.0/", 
      "id": 2, 
      "name": "Attribution-NonCommercial License" 
    }, 
    ... 
  ], 
  "images": [ 
    { 
      "license": 4, 
      "file_name": "000000397133.jpg", 
      "coco_url": "http://images.cocodataset.org/val2017/000000397133.jpg", 
      "height": 427, 
      "width": 640, 
      "date_captured": "2013-11-14 17:02:52", 
      "flickr_url": "http://farm7.staticflickr.com/6116/6255196340_da26cf2c9e_z.jpg", 
      "id": 397133 
    }, 
    { 
      "license": 1, 
      "file_name": "000000037777.jpg", 
      "coco_url": "http://images.cocodataset.org/val2017/000000037777.jpg", 
      "height": 230, 
      "width": 352, 
      "date_captured": "2013-11-14 20:55:31", 
      "flickr_url": "http://farm9.staticflickr.com/8429/7839199426_f6d48aa585_z.jpg", 
      "id": 37777 
    }, 
    ...
  ],
  "annotations" : [
    {
      "segmentation": [ 
        [ 83.22, 
          632.06, 
          1.51, 
          627.52, 
          1.51, 
          509.5, 
          0, 
          453.52, 
          102.88, 
          ..., 
          627.52 ] 
      ], 
      "area": 136955.75629999995, 
      "iscrowd": 0, 
      "image_id": 17899, 
      "bbox": [ 0, 279.53, 480, 352.53 ], 
      "category_id": 67, 
      "id": 119334 
    }, 
    ...
  ]

~~~

이제 우리의 COCO 데이터 포맷을 YOLO 형식으로 변환해주어야 합니다. 그래야 YOLO 시스템을 이용해 학습을 시킬 수
있을테니 말이죠.
<br><br>

COCO 데이터 포맷은 위에서 확인할 수 있듯이 bbox 값이 x, y, w, h 값으로 구성되어있습니다. 하지만 YOLO 에서의 포맷은
클래스 번호와 전체 영상(이미지) 크기에 대한 center x, center y, w, h 비율 값으로 구성됩니다. 또한 COCO는 모든 영상의 주석이
담겨진 하나의 json 파일로 구성되는데 YOLO는 한 영상(이미지) 당 한 개의 txt 파일로 구성되어있습니다.
<br><br>

이 변환은 Github에 공유되어 있는 프로그램을 이용해 변환하는게 속이 편합니다. 아니면 코드를 짜야되니까요.
<br><br>

[Convert2YOLO](https://github.com/ssaru/convert2Yolo){: target="_blank" }
<br><br>

위의 git 저장소에서 파일을 다운로드 한 후 압축을 푸는 방법과 다음과 같이 git clone을
하는 방법 중 편한대로 하시면 됩니다. (현재 working directory는 `C:/YOLO_Object_Detection` 입니다.)

> `git clone https://github.com/ssaru/convert2Yolo.git`

<br>

다음의 명령으로 실행시켜 데이터를 변환합니다. 파일이 많아서 시간이 상당히
오래 걸립니다. 참고 기다려보죠!!
<br><br>

참고로 `requirements.txt` 파일을 참조하여 필요한 package를 설치하라는 말이 있는데
이걸 설치하면 버전문제로 다른곳에서 오류가 발생할 여지가 있으니 설치하지는 않는게
좋을듯 합니다.
<br><br>

복사 붙여넣기를 위해서 한줄로 표현하면 다음과 같습니다. 내용은 아래에서 다시 설명합니다.
시간이 오래걸립니다. 
<br><br>

> `python example.py --datasets COCO --img_path /home/tutor/YOLO_Object_Detection/coco/images/train2017/ --label /home/tutor/YOLO_Object_Detection/coco/annotations/instances_train2017.json --convert_output_path /home/tutor/YOLO_Object_Detection/convertToYolo/ --img_type ".jpg" --manifest_path /home/tutor/YOLO_Object_Detection/manifest/ --cls_list_file /home/tutor/YOLO_Object_Detection/coco/coco80.names`

<br>

> $ python example.py<br>
>   `--datasets` COCO<br>
>   `--img_path` /home/tutor/YOLO_Object_Detection/coco/images/train2017/<br>
>   `--label` /home/tutor/YOLO_Object_Detection/coco/annotations/instances_train2017.json<br>
>   `--convert_output_path` /home/tutor/YOLO_Object_Detection/convertToYolo/<br>
>   `--img_type` ".jpg"<br>
>   `--manifest_path` /home/tutor/YOLO_Object_Detection/manifest/<br>
>   `--cls_list_file` /home/tutor/YOLO_Object_Detection/coco/coco80.names

각 option의 의미는 다음과 같습니다.
<br><br>

* `img_path`
: val2017 이나 train2017을 다운받은 폴더

* `label`
: 해당 데이터에 대한 주석 파일

* `convert_output_path`
: txt파일들을 저장할 공간

* `img_type`
: COCO 영상 확장자

* `manifest_path`
: 변환된 파일들의 리스트를 manifest.txt로 저장할 폴더

* `cls_list_file`
: COCO 클래스 이름들이 담긴 파일

여기서 cls_list_file 은 다음과 같은 리스트를 이용하였습니다.
darknet git에 포함되어있는 `coco.names`의 이름과 원래 coco 데이터 상 이름이 약간 상이하기 때문에
아래와 같이 수정해서 사용합니다.

~~~python

person
bicycle 
car
motorcycle 
airoplane 
bus
train 
truck 
boat
traffic light 
fire hydrant
stop sign
parking meter
bench
bird
cat
dog
horse
sheep
cow
elephant
bear
zebra
giraffe
backpack
umbrella
handbag
tie
suitcase
frisbee
skis
snowboard
sports ball
kite
baseball bat
baseball glove
skateboard
surfboard
tennis racket
bottle
wine glass
cup
fork
knife
spoon
bowl
banana
apple
sandwich
orange
broccoli
carrot
hot dog
pizza
donut
cake
chair
sofa
potted plant
bed
dining table
toilet
tv
laptop
mouse
remote
keyboard
cell phone
microwave
oven
toaster
sink
refrigerator
book
clock
vase
scissors
teddy bear
hair drier
toothbrush

~~~

이 과정을 거치면 YOLO를 학습시킬 수 있는 train dataset을 생성할 수 있습니다.

---

## PASCAL VOC Data Set

YOLO를 학습시킬 수 있는 train dataset을 만들기 위해 하나만 더 해보도록 하죠.
<br><br>

위에서 설명한 `Convert2YOLO`를 이용해 `PASCAL VOC data set` 역시 convert할 수
있습니다. 아래처럼 하시면 됩니다.
<br><br>

먼저 `VOC` 폴더를 하나 생성한 후 그 안에 `VOCdevkit`을 복사합니다.
그러면 아래 그림과 같은 형태로 폴더 구조가 생성됩니다.
<br><br>

데이터셋을 다운로드 받기 위해서는 아래의 명령을 사용하시면 됩니다.

~~~python

# current working directory : C:/YOLO_Object_Detection

mkdir VOC

cd VOC

wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar

tar -xvf VOCtrainval_11-May-2012.tar

~~~

{% include image.html
file='machine-learning-voc-directory-structure.png'
%}
<br>

우리는 여기서 `Annotations`과  `JPEGImages`만 이용합니다.
<br><br>

> `Annotations` : Object Detection label folder (*.xml)

> `JPEGImages` : Image data (*.jpg)

<br>

이제 `voc.names` 파일을 `VOC` 디렉토리 안에 생성합니다.
<br><br>

파일의 내용은 다음과 같습니다.

~~~python

aeroplane
bicycle
bird
boat
bottle
bus
car
cat
chair
cow
diningtable
dog
horse
motorbike
person
pottedplant
sheep
sofa
train
tvmonitor

~~~

`VOC` 디렉토리에 `VOC_to_YOLO` 디렉토리를 생성한 후 다음의 코드를 실행합니다.
(다음 명령의 수행은 `convert2Yolo` 폴더안에서 수행해야 합니다.)
<br><br>

> `python example.py --datasets VOC --img_path C:/YOLO_Object_Detection/VOC/VOCdevkit/VOC2012/JPEGImages/ --label C:/YOLO_Object_Detection/VOC/VOCdevkit/VOC2012/Annotations/ --convert_output_path C:/YOLO_Object_Detection/VOC/VOC_to_YOLO/ --img_type ".jpg" --manifest_path C:/YOLO_Object_Detection/VOC/ --cls_list_file C:/YOLO_Object_Detection/VOC/voc.names`

정상적으로 수행되면 다음과 같은 화면을 볼 수 있습니다.

{% include image.html
file='machine-learning-voc-dataset-convert-result.png'
%}
<br>

이 과정까지 성공하면 YOLO를 학습시킬 수 있는 `VOC train dataset`을 생성할 수 있습니다.
<br><br>

추가적으로 자신이 원하는 데이터를 수집한 후 이를 이용해 YOLO를 학습 시킬 수도 있습니다.
데이터 크롤링이나 오픈 데이터셋을 이용해서 이미지나 영상을 수집한 후 필요한 경우 `labeling` 처리를
해야 하며 YOLO 포맷으로 변환하는 과정을 거쳐야 합니다.
<br><br>

크롤링한 데이터를 `labeling`할 때는 `labelImg`라는 유명한 이미지 labeling 툴을 이용하면 편하게 작업할 수
있습니다. (`labelImg`를 사용하는 방법은 아래쪽에 따로 기술하였습니다.)

---

## YOLOv4 학습(VOC)

데이터가 준비되면 이제 Darknet을 학습시킬 수 있습니다. 위에서 준비한 `VOC train dataset`을
이용해서 학습시키는 작업을 진행해보고 뒤쪽에서는 수집한 이미지를 이용해서 추가로 학습하는 과정에
대해 알아보도록 하겠습니다.
<br><br>

* 먼저 학습에 사용할 `pre-trained model`의 `weights` 파일을 다운받도록 하죠.
    * 여기서는 `yolov4.conv.137` 가중치 파일을 사용합니다. 가중치 파일은 아래의 링크에서
      다운로드 하시면 됩니다. 다운로드 한 후 해당 파일을 `C:/YOLO_Object_Detection/darknet`에
      위치시킵니다.
    * [yolov4.conv.137](https://drive.google.com/file/d/1VxqabWg8l410JjIerkramtxxfkRmHlKB/view?usp=sharing){: target="_blank" }
      > `wget https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.conv.137`

* `config` 파일을 생성합니다.
    * `cfg`디렉토리 안에 있는 `yolov4-custom.cfg` 파일을 복사해서 `yolov4-voc.cfg`를 생성합니다.
    * `cp cfg/yolov4-custom.cfg cfg/yolov4-voc.cfg`
    * 모델 구조 및 학습과 관련된 설정이 들어있는 파일입니다.
    * `batch` : 한번에 처리할 이미지 수(batch=64로 설정합니다.)
    * `subdivisions` :  batch / subdivisions 씩 묶어서 처리(subdivisions=64로 설정합니다.)
    * `width`와 `height`는 기본이 608,608로 잡혀있는데 이 사이즈가 크면 GPU메모리를
      많이 사용하게 되므로 사이즈를 약간 줄여서 사용하는게 좋습니다.(416,416)
    * `max_batches` : 현재 class개수 * 2000 으로 설정(40000)
    * `steps` : max_batches의 80% 수치와 90%의 수치로 작성(32000,36000)
    * 만약 학습이 불충분하다고 생각되면 `max_batches`에 원하는 iteration 수를 입력합니다.
    * `yolo`를 검색하면 총 3개가 검색되는데 이는 레이어가 총 3개라는 것을 의미합니다.
      3개의 레이어가 있는 경우에 총 6개의 수정이 필요한데 첫번째는 모든 레이어에 대해서
      class수를 수정(3개)해줘야 하고 두번째로 레이어 위에 있는 레이어의 filter 수를
      수정(3개)해야 합니다. 수정할 filter 수는 `(5 + class 수) * 3`으로 설정합니다.
      이 때 `[yolo]` 바로 위에 있는 `[convolutional]` 안에 위치한 `filters`만 수정해주면 됩니다.
    * `filters` : (5 + class 수) * 3 으로 설정합니다.(75)
    * `classes` : class 수 설정(20)
    * 아래는 `cfg/yolov4-voc.cfg` 파일의 내용 첫부분 입니다.

~~~python

[net]
# Testing
#batch=1
#subdivisions=1
# Training
batch=64
subdivisions=64  # 16으로 할경우 OOM 오류가 발생합니다.
#width=608
#height=608
width=416  # OOM 오류가 발생할 경우 사이즈를 줄입니다.
height=416
channels=3
momentum=0.949
decay=0.0005
angle=0
saturation = 1.5
exposure = 1.5
hue=.1

learning_rate=0.001
burn_in=1000
max_batches = 40000
policy=steps
steps=32000,36000
scales=.1,.1

#cutmix=1
mosaic=1

...

~~~

* `my_voc.names` 파일 복사(`C:/YOLO_Object_Detection/darknet/data/my_voc.names`)
    * 각 line에 object의 이름이 들어있는 파일입니다.
    * 기존에 생성했던 파일을 복사해서 사용합니다.
    * 즉, VOC 폴더안의 `voc.names`파일의 이름을 변경하여 해당 폴더로 복사합니다.
    * 아래는 `data/my_voc.names` 파일의 내용입니다.

~~~python

aeroplane
bicycle
bird
boat
bottle
bus
car
cat
chair
cow
diningtable
dog
horse
motorbike
person
pottedplant
sheep
sofa
train
tvmonitor

~~~

* 학습에 사용될 이미지를 `data/voc` 디렉토리에 복사합니다.
    * `VOC dataset` 이미지들과 각 이미지 파일에 대한 label txt파일을 복사합니다.
    * `cp -r ~/VOC/VOCdevkit/VOC2012/JPEGImages/ data/voc`
    * `cp ~/VOC/VOC_to_YOLO/*.txt data/voc`
    * 아래의 이미지는 `data/voc` 디렉토리 안의 내용 캡쳐입니다.

{% include image.html
file='machine-learning-yolo4-training-img_txt.png'
%}
<br>

* `my_voc.data` 파일생성(`data/my_voc.data`)
    * 학습을 위한 내용이 담겨있는 파일입니다.
    * classes 개수를 설정합니다.
    * `train.txt`와 `test.txt`의 경로를 포함합니다.
    * `train.txt`파일은 VOC dataset을 생성할 때 만들었던 `manifest.txt`을 의미합니다.
    * `cp ~/VOC/manifest.txt data/my_manifest.txt`
    * `my_manifest.txt`파일안의 이미지 경로를 현재 우리 환경에 맞게 수정합니다.
    * `vi`를 이용하여 다음의 명령을 이용하면 간편하게 변환할 수 있습니다.

~~~python
%s /VOC\/VOCdevkit\/VOC2012\/JPEGImages/YOLO_Object_Detection\/darknet\/data\/voc    
~~~

* `my_voc.names`의 경로를 설정합니다.
* 학습이 종료된 후 결과 `weights` 파일이 저장될 경로를 설정합니다.(디렉토리를 미리 생성해 놓아야 합니다.)
* 아래는 `data/my_voc.data` 파일의 내용입니다.

~~~python

classes = 20
train = data/my_manifest.txt
names = data/my_voc.names
backup = voc_backup/

~~~

`data/my_manifest.txt` 파일의 내용은 다음과 같이 수정되어야 합니다.

{% include image.html
file='machine-learning-yolo4-my_manifest.txt.png'
%}
<br>

여기까지 진행됬으면 이제 학습을 진행합니다.
(`darknet/voc_backup` directory가 생성되어 있어야 합니다.)
<br><br>

> `./darknet detector train data/my_voc.data cfg/yolov4-voc.cfg yolov4.conv.137 -dont_show`

<br>

만약 다음과 같은 오류가 발생하면 메모리 부족에 대한 문제이므로 cfg 파일을 수정해 주면 됩니다.
<br><br>

> `Error: cuDNN isn't found FWD algo for convolution.`

<br>

만약 `opencv` 관련 오류가 발생하면 `darknet` 안의 `Makefile`의 opencv option을
0에서 1로 변경하고 다시 `build`작업을 진행한 후 학습을 진행하시면 됩니다.
<br><br>

정상적으로 수행되면 다음과 같이 학습이 진행되는 것을 확인할 수 있습니다.

{% include image.html
file='machine-learning-yolo4-training.png'
%}

학습이 완료되면 `voc_backup` 디렉토리에 결과 `weights` 파일이 저장되어 있는걸
확인할 수 있습니다.
<br><br>

다음의 이미지를 이용해서 기본 yolov4로 detection 한 결과와 이번에 VOC dataset으로
학습한 yolov4로 detection한 결과를 비교해 보도록 하겠습니다.
<br><br>

사용할 이미지는 다음과 같습니다.

{% include image.html
file='machine-learning-yolo4-riders.jpg'
%}
<br>

기본 yolov4로 detection 한 결과는 다음과 같습니다.
<br><br>

> `./darknet detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights riders.jpg`

{% include image.html
file='machine-learning-yolo4-riders-prediction1.jpg'
%}
<br>

VOC dataset으로 학습한 yolov4로 detection한 결과는 다음과 같습니다.
<br><br>

> `./darknet detector test data/my_voc.data cfg/yolov4-voc.cfg voc_backup/yolov4-voc_final.weights riders.jpg`

{% include image.html
file='machine-learning-yolo4-riders-prediction2.jpg'
%}
<br>

---

## YOLOv4 학습(Custom)

`YOLO`를 이용해 다음의 사진을 detection 해 보도록 하겠습니다.
<br><br>

{% include image.html
file='machine-learning-yolo4-test-image-fire.jpg'
%}
<br>

현재 `YOLO4`는 `fire(불)`에 대한 detection을 할 수 없습니다. 실제로 COCO Dataset으로 학습된 기본 YOLO4를 실행시켜
결과이미지를 한번 보도록 하죠.
<br><br>

> `./darknet detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights fire.jpg`

<br>

실행결과는 다음과 같습니다.

{% include image.html
file='machine-learning-yolo4-test-image-fire-result.jpg'
%}
<br>

어떻게 하면 `fire(불)`을 인식시킬 수 있을까요?
<br><br>

우리는 이 작업을 하기 위해 새로운 데이터셋을 이용해서 `labeling`작업을 한 후 학습을 시켜야 합니다.
<br><br>

`labeling`작업은 위에서 살짝 언급한 `labelImg`를 사용해 보겠습니다.
<br><br>

`Windows`인 경우 아래의 링크로 접속합니다. 
<br><br>

[labelImg GitHub Repository](https://github.com/heartexlabs/labelImg){: target="_blank" }
<br><br>

우측에 `Release` 를 클릭해서 Windows Binary파일을 다운로드 하면 됩니다. 
<br><br>

아래의 링크에서 다운받아도 됩니다 .
<br><br>

[labelImg (Google Drive)](https://drive.google.com/file/d/1Ef17mT6MIObzFU5TVgYv-IPQZ4KSPy8o/view?usp=sharing){: target="_blank" }
<br><br>

`AWS EC2`인 경우 conda 가상환경에서 아래의 내용을 수행하시면 됩니다.
<br><br>

다음의 명령을 이용해 `labelImg`를 설치합니다.
<br><br>

> pip install labelImg

<br>

설치가 완료되면 `.local/bin`안에 설치가 됩니다. 해당 폴더에서
다음의 명령을 이용해 실행시킵니다.
<br><br>

> labelImg

<br>

여기서 한가지 문제가 발생하는데 만약 `SSH`를 이용해 원격으로
`AWS EC2`에 `Putty`를 이용해 접속해서
사용하고 있는 경우 별도의 설정을 해 주지 않으면 당연히 `display`를 찾지
못한다는 오류와 함께 GUI 프로그램은 실행되지 않습니다.
<br><br>

일단 아래의 링크를 이용해 `Xming`을 설치합니다.
display 설정을 바꿔주는 `X server`입니다.
<br><br>

[Xming download](https://sourceforge.net/projects/xming/){: target="_blank" }
<br><br>

설치하고 실행시키면 됩니다. 그리고 `putty`를 실행시킬 때 한가지 설정을 더 해 줘야 합니다. 아래의
그림처럼 `X11 forwarding` option을 활성화 시켜야 합니다.

{% include image.html
file='machine-learning-putty-x11-forwarding.png'
%}
<br>

여기까지 했으면 putty를 이용해 서버로 접속한 후 다음의 명령을 이용해 필요한 package를 설치하고
환경변수를 설정합니다.
<br><br>

이 내용은 아래의 링크에서 발췌한 내용입니다.
<br><br>

[Error with Qt platform plugin "xcb" when start labelImg](https://github.com/tzutalin/labelImg/issues/630){: target="_blank" }
<br><br>

> `sudo apt-get update`

> `sudo apt-get install pyqt5-dev-tools`

> `export QT_DEBUG_PLUGINS=1`

<br>

이렇게 한 다음 `labelImg`를 실행시키면 다음과 같은 화면이 우리 windows에서 실행되는것을 볼 수 있습니다.

{% include image.html
file='machine-learning-xming-labelimg.png'
%}
<br>

사용하는 방법은 아주 간단합니다. 메뉴를 보고 그림에 사각형을 그린다음 label을 지정하면
됩니다.

{% include image.html
file='machine-learning-labelimg-fire.png'
%}
<br>

다수의 이미지를 구해 labeling을 한 후 Dataset을 이용해 `YOLO`를 학습 시킨 후
위의 사진을 다시 한번 detection 해 보기로 하죠.
<br><br>

이미지를 구하고 labeling을 직접하는건 시간이 오래 걸리니 다음의 링크에서 화재이미지와
PASCAL VOC Dataset 형태로 되어 있는 xml 파일을 다운로드 받아서 YOLO Dataset으로
변환시켜서 학습에 이용하도록 하겠습니다.
<br><br>

[FireNet - AI project for real-time fire detection](https://github.com/OlafenwaMoses/FireNET){: target="_blank" }
<br><br>

[FireNet DataSet Download](https://github.com/OlafenwaMoses/FireNET/releases/download/v1.0/fire-dataset.zip){: target="_blank" }
<br><br>

[FireNet DataSet - Google Drive Download](https://drive.google.com/file/d/11c3lMEbiMm3jOfChqe1DpsAd22msUAiI/view?usp=sharing){: target="_blank" }
<br><br>

`FireNet DataSet`을 다운로드 받은 후 `Convert2YOLO`를 이용해 Yolo Data Format으로 변환시킵니다.
<br><br>

`FireNet DataSet`을 다운받아서 압축을 풀면 다음과 같은 디렉토리에 train, validation 디렉토리가 생성됩니다.

~~~python

# current working directory : C:/YOLO_Object_Detection

unzip fire-dataset.zip

~~~

이제 `fire.names` 파일을 `C:/YOLO_Object_Detection/fire-dataset` 디렉토리 안에 생성합니다.
<br><br>

파일의 내용은 다음과 같습니다.

~~~python

fire

~~~

fire-dataset 디렉토리에 `VOC_to_YOLO_TRAIN`과 `VOC_to_YOLO_VALID`, `manifest/train`, `manifest/valid`
디렉토리를 생성한 후 다음의 코드를 각각 실행합니다.
<br><br>

> `python example.py --datasets VOC --img_path C:/YOLO_Object_Detection/fire-dataset/train/images/ --label C:/YOLO_Object_Detection/fire-dataset/train/annotations/ --convert_output_path C:/YOLO_Object_Detection/fire-dataset/VOC_to_YOLO_TRAIN/ --img_type ".jpg" --manifest_path C:/YOLO_Object_Detection/fire-dataset/manifest/train/ --cls_list_file C:/YOLO_Object_Detection/fire-dataset/fire.names`

> `python example.py --datasets VOC --img_path C:/YOLO_Object_Detection/fire-dataset/validation/images/ --label C:/YOLO_Object_Detection/fire-dataset/validation/annotations/ --convert_output_path C:/YOLO_Object_Detection/fire-dataset/VOC_to_YOLO_VALID/ --img_type ".jpg" --manifest_path C:/YOLO_Object_Detection/fire-dataset/manifest/valid/ --cls_list_file C:/YOLO_Object_Detection/fire-dataset/fire.names`

<br>

이제 필요한 파일들을 위에서 살펴본 VOC 학습방법을 참조하여
구조에 맞게 복사, 생성한 후 다음의 명령으로 학습을 진행합니다. `-dont_show` 옵션을 생략하면 그래프로 loss의 값을
확인할 수 있습니다. 단, `AWS EC2`에서는 `X Server Display` 오류가 발생할 수 있습니다.
이런 경우 `Windows 10`에서 실행시키면 그래프도 확인이 가능합니다.

{% include image.html
file='machine-learning-darknet-train-graph-fire.png'
%}
<br>

다음은 `my_fire.data`의 내용입니다.

~~~python

classes = 1
train = data/my_fire_train_manifest.txt
valid = data/my_fire_valid_manifest.txt
names = data/my_fire.names
backup = fire_backup/

~~~

다음은 `my_fire.names`의 내용입니다.

~~~python

fire

~~~

다음은 `yolov4-fire.cfg`의 내용입니다.

~~~python

[net]
batch=64
subdivisions=64
# Training
#width=512
#height=512
width=608
height=608
channels=3
momentum=0.949
decay=0.0005
angle=0
saturation = 1.5
exposure = 1.5
hue=.1

learning_rate=0.0013
burn_in=1000
max_batches = 2000
policy=steps
steps=1600,1800
scales=.1,.1
...

[convolutional]
size=1
stride=1
pad=1
filters=18       # filter의 개수를 18로 설정 => (1 + 5) * 3
activation=linear

[yolo]
mask = 0,1,2
anchors = 12, 16, 19, 36, 40, 28, 36, 75, 76, 55, 72, 146, 142, 110, 192, 243, 459, 401
classes=1        # class의 수는 1
num=9
jitter=.3

...

~~~

이제 학습을 진행합니다. 
<br><br>

> `./darknet detector train data/my_fire.data cfg/yolov4-fire.cfg yolov4.conv.137 -dont_show`

<br>

학습이 끝나면 생성된 `weight` 파일을 이용하여 예제로 사용된 화재사진을 prediction 합니다.
<br><br>

> `./darknet detector test data/my_fire.data cfg/yolov4-fire.cfg fire_backup/yolov4-fire_final.weights fire.jpg`

<br>

최종 실행 결과는 다음과 같습니다.

{% include image.html
file='machine-learning-yolo-fire-detection-result.png'
%}

---

## YOLOv4 동영상 Object Detection

동영상도 탐지가 가능합니다. 동영상 파일을 입력으로 주면 Detection 처리가 된
동영상을 결과로 생성할 수 있습니다.
<br><br>

custom 학습한(fire)를 이용하여 동영상 Detection을 해 보도록 하죠.
<br><br>

동영상은 다음의 링크에서 다운 받으시면 됩니다.
<br><br>

[Fire Movie - Google Drive Download](https://drive.google.com/file/d/12luJO_7h3sk0GsjliOwtH7wK9wx5r2Zm/view?usp=sharing){: target="_blank" }
<br><br>

실행은 다음의 명령을 실행하시면 됩니다.
<br><br>

> `./darknet detector demo data/my_fire.data cfg/yolov4-fire.cfg fire_backup/yolov4-fire_final.weights ~/fire-movie.mp4 -out_filename ~/fire-movie-result.mp4 -dont_show`

<br>

결과 동영상은 다음 링크에서 받으실 수 있습니다.
<br><br>

[Fire Movie(Object Detection Result) - Google Drive Download](https://drive.google.com/file/d/1a1x9EnRM1ztnRXHyCotbWsgZATB0UHqy/view?usp=sharing){: target="_blank" }
<br><br>

End.

{% include links.html %}
