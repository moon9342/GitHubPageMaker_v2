---
title: Object Detection CUDA & cuDNN 설치
sidebar: object_detection_sidebar
summary: "Object Detection을 위한 CUDA와 cuDNN설치에 대한 내용입니다."
permalink: object_detection_cuda-cudnn.html
folder: object_detection
---

실행환경은 두가지 입니다. 첫번째는 `Windows` 환경이고 두번째는 `AWS EC2`입니다.

## Windows

먼저 원활한 수행을 위해 Windows에 `CUDA`와 `cuDNN`을 설치하도록 하겠습니다. 현재 사용하는 그래픽 카드는
`NVIDIA GeForce RTX 3070 Ti`입니다.
<br><br>

먼저 다음의 링크를 이용하여 CUDA를 다운로드 합니다.
<br><br>

[CUDA Download](https://developer.nvidia.com/cuda-downloads){: target="_blank" }
<br><br>

target platform을 설정하면 `cuda_11.8.0_522.06_windows.exe`을 다운로드 할 수 있습니다. 다운로드 후 설치를 진행합니다.
<br><br>

아래의 링크에서도 다운로드 할 수 있습니다.
<br><br>

[CUDA 11.8.0_522.06_windows Download(Google Drive)](https://drive.google.com/file/d/1wXWnLfo40vpCSTzhV3APQ9_ifu6GAuZf/view?usp=sharing){: target="_blank" }
<br><br>

설치가 끝나면 `CUDA_PATH`, `CUDA_PATH_V11_8` 환경변수와 시스템 환경변수 `PATH`에 CUDA 경로가 자동으로 설정됩니다.
<br><br>

이제 다음의 링크를 클릭하여 `zlib123dllx64.zip`을 다운로드 합니다.
<br><br>

[zlib123dllx64 Download](https://drive.google.com/file/d/1COdQYd4A3PvsYNHiIR-BslZHU5SlFMmd/view?usp=sharing){: target="_blank" }
<br><br>

압축을 풀고 `zlibwapi.dll` 파일을 찾아 CUDA의 `bin` 폴더에 복사합니다.
기본으로 설정된 경로는 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8\bin` 입니다.
<br><br>

다음으로 `cuDNN`을 다운로드 한 후 설치합니다.
<br><br>

[cuDNN Download](https://developer.nvidia.com/rdp/cudnn-download#a-collapse714-92){: target="_blank" }
<br><br>

단, `cuDNN`을 다운로드 받기 위해서는 NVIDIA 계정이 있어야 합니다. 로그인을 진행한 후
`cuDNN v8.6.0 (October 3rd, 2022), for CUDA 11.x`를 다운로드 합니다. 아래의 링크를 클릭해도 됩니다.
<br><br>

[cuDNN v8.6.0 Download(Google Drive)](https://drive.google.com/file/d/12Ou-betsqXTTF2lLEOXvTU7MzTkEYKtS/view?usp=sharing){: target="_blank" }
<br><br>

다운로드가 완료되면 압축을 해제합니다. 압축을 해제하면 그 안에 `bin`, `include`, `lib` 폴더가 있는것을 확인할 수 있는데
이 3개의 폴더를 CUDA 설치폴더(`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8`)에 복사하면 됩니다. 

---

## AWS EC2

만약 `AWS EC2`를 이용할 경우 아래의 내용대로 `CUDA`와 `cuDNN`을 설치하시면 됩니다.
<br><br>

현재 AWS EC2에 설치되어 있는 CUDA버전은 다음의 명령을 실행하면 알 수 있습니다.
<br><br>

> `nvcc -V`

<br>

사용하고 있는 AWS EC에 설치된 CUDA 버전은 11.4입니다. `Colab`을 사용하는 경우
`!nvcc -V`를 실행하면 알 수 있습니다.
<br><br>

참고로 `nvidia-smi`를 실행시키면 결과 출력 표에서 `CUDA Version: 11.x`라는
출력을 볼 수 있는데 이 내용은 현재 해당 버전이 설치되어 있다는 의미가 아니라
해당 버전이 필요하다는 의미입니다.
<br><br>

또한 cuDNN 버전도 확인해야 하는데 cuDNN 8.x 버전 이상인 경우 다음의 명령을 이용하면
확인할 수 있습니다.(AWS EC2인 경우)
<br><br>

> `cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2`

~~~text

#define CUDNN_MAJOR 8
#define CUDNN_MINOR 2
#define CUDNN_PATCHLEVEL 2

~~~

이렇게 출력되는데 현재 버전이 `8.2.2` 인 것을 확인할 수 있습니다.
<br><br>

만약 `Google Colab`인 경우 다음의 코드를 이용해서 버전을 확인하시면 됩니다. `pytorch`를
이용한 코드입니다.

~~~python

import torch

print("cuda version: {}".format(torch.version.cuda))
print("cudnn version:{}".format(torch.backends.cudnn.version()))

# cuda version: 11.3
# cudnn version:8200

~~~

뒤쪽에서 `pre-trained network`의 `checkpoint`를 이용해서 처리해야 하는데 이 파일들이
`cuDNN 8.1`버전을 요구합니다. 그래서 만약 설치되어 있는 cuDNN이 `8.1`보다 낮다면
cuDNN 8.1 버전으로 upgrade한 후 진행해야 합니다.
<br><br>

그럼 AWS EC2에 `CUDA`와 `cuDNN`을 버전에 맞게 설치해 보도록 하죠.
<br><br>

주의해야 할 점은 `dependency`입니다. 버전을 서로 맞춰서 설치해야 정상적으로 실행이 됩니다.
<br><br>

[NVIDIA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html){: target="_blank" }
사이트에 접속해 보시면 아래와 같은 matrix를 보실 수 있습니다.
<br><br>

{% include image.html
file='deep-learning-aws-cuda-dependency-matrix.png'
%}
<br>

우리가 사용하려는 nvidia driver 버전은 `470.57.02`이기 때문에 그에 맞는 CUDA버전과 cuDNN버전을
선택해서 설치해야 합니다.
<br><br>

2022년 05월 기준 우리가 사용하고 있는 `Ubuntu 18.04` 버전에
`CUDA 11.4.2`과 `cuDNN 8.2.2`버전을 설치하려 합니다.
<br><br>

시스템 설정을 해야 하기 때문에 `root`권한을 가지고 있는 계정으로 로그인 한 후 아래의 내용을
진행해야 합니다. 
<br><br>

일단 현재 사용하는 AWS EC2 Ubuntu의 버전을 확인합니다.
<br><br>

> `cat /etc/issue`

<br>

우리가 현재 사용하는 Ubuntu의 버전은 `Ubuntu 18.04.5 LTS \n \l`로 출력됩니다.
<br><br>

이번에는 현재 설치되어 있는 NVIDIA CUDA 버전을 살펴보죠.
<br><br>

> `nvcc -V`

<br>

Cuda compilation tools, `release 10.0`, V10.0.130
<br><br>

현재 시스템에는 CUDA가 10.0이 설치되어 있습니다.
<br><br>

`CUDA`는 GPU 서버를 사용하기 위해서는 필수 중 하나입니다.
GPU 서버를 셋팅하기 위해서는 기본적으로 `Nvidia Driver` 와 `CUDA`가 설치가 되어 있어야 하며
`cuDNN`등이 추가로 설치 되어야 합니다.
<br><br>

그 위에 딥러닝 프레임워크등이 올라가는데 예전에는 `Tensorflow`, `Pytorch`등 많은 것들을
설치해서 사용했었습니다.
<br><br>

하지만 여러 사용자가 각기 다른 프레임워크에 version 또한 다르다 보니
요즘에는 `Docker`, `nvidia-docker`, `Anaconda`등을 사용하고 있는 상황입니다.
<br><br>

참고로 기존에 `CUDA`와 `NVIDIA Driver`가 설치되어 있을 경우 다음의 명령을
이용하면 삭제할 수 있습니다.

~~~python

# NVIDIA Driver 삭제
> sudo nvidia-uninstall
> sudo apt-get purge nvidia*
> sudo apt-get autoremove
> sudo apt-get autoclean

# NVIDIA Driver 삭제 확인
> sudo dpkg -l | grep nvidia (출력이 없어야 한다)
> sudo apt-get remove --purge 지울이름 (만약 출력이 있으면 수동으로 삭제)


# CUDA 삭제
> cd /usr/local/cuda-11.4/bin
> sudo ./cuda-uninstaller
> sudo rm -rf /usr/local/cuda*

# CUDA 삭제 확인
> sudo dpkg -l | grep cuda (출력이 없어야 한다)
> sudo apt-get remove --purge 지울이름 (만약 출력이 있으면 수동으로 삭제)

# 반드시 reboot
> sudo reboot 

~~~

`CUDA 11.4.2`를 다운받아 설치해보도록 하죠.
<br><br>

다음의 링크를 이용해서 설치하고자 하는 CUDA version을 다운로드 합니다.
<br><br>

[CUDA download](https://developer.nvidia.com/cuda-toolkit-archive){: target="_blank" }
<br><br>

사용하는 시스템에 맞게 몇가지 option들을 선택하면 아래와 같은 다운로드 할 수 있는
명령어를 확인할 수 있습니다.

{% include image.html
file='deep-learning-aws-cuda-install_0.png'
%}
<br>

해당 파일을 다운로드합니다.
<br><br>

설치를 진행하기에 앞서 현재 시스템에 설치되어 있는 NVIDIA version을 먼저 확인해보도록 하죠.
<br><br>

현재 시스템에 설치되어 있는 NVIDIA version을 확인하려면 다음의 명령을 이용하시면 됩니다.
<br><br>

> `cat /proc/driver/nvidia/version`

<br>

만약 설치되어 있지 않다면 다음의 메시지를 보게 될 것이고 그렇지 않다면 버전 정보가
출력됩니다.
<br><br>

> cat: /proc/driver/nvidia/version: No such file or directory

<br>

또는
<br><br>

> NVRM version: NVIDIA UNIX x86_64 Kernel Module  470.74  
> Mon Sep 13 23:09:15 UTC 2021
> GCC version:  gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)

<br>

CUDA를 설치할 때 NVIDIA Driver도 같이 설치할 수 있지만 드라이버 버전이
낮을 수 있기 때문에 CUDA 설치 전 미리 repository 등록하여
NVIDIA Driver를 apt-get install 로 설치 하는 것도 하나의 방법입니다.
(Object Detection API를 사용할 때는 가능한 최신버전을 이용하는게 좋을 듯 합니다.)
<br><br>

여기서 살짝 문제가 발생할 수 있습니다. 원래 nvidia driver를 제거하고 reboot를 한 후
`cat /proc/driver/nvidia/version`을 실행하면 드라이버 정보가 나오지 말아야 하는데
서버 설정상 nvidia-driver가 남아 있을 수 있습니다. 이 경우 kernel에서 driver를 unloading
해야하는데 이 방법을 찾느니 그냥 다음 과정을 수행해서 driver 버전을 `470`으로 upgrade 한 후
드라이버를 제거하고 `cuda`를 설치하시는게 속이 편할 듯 합니다.
<br><br>

다음은 `nvidia-driver`를 upgrade하는 방법입니다.
<br><br>

먼저 다음 repository를 추가합니다.
<br><br>

> `sudo add-apt-repository ppa:graphics-drivers/ppa`

> `sudo apt update`

<br>

apt-cache search는 설치 가능한 드라이버 목록을 출력합니다.
<br><br>

> `apt-cache search nvidia | grep nvidia-driver-470`

<br>

이제 apt로 드라이버를 설치합니다.
<br><br>

> `sudo apt-get install nvidia-driver-470`

<br>

설치가 완료되면 reboot을 합니다.
<br><br>

> `sudo reboot`

NVIDIA Driver 설치가 다 되었으면 이제 위쪽에 있는 내용을 이용하여
nvidia-driver를 uninstall한 후 CUDA 설치파일을
다운로드 한 후 다음과 같이 실행시킵니다.
<br><br>

> `sudo chmod 777 cuda_11.4.2_470.57.02_linux.run`

> `sudo ./cuda_11.4.2_470.57.02_linux.run`

<br>

한참(?)을 기다리면 `End User License Agreement` 화면이 나오게 되고 당연히
`accept`한 후 진행해야 합니다.
<br><br>

그러면 `CUDA Installer`가 실행되면서 다음과 같은 화면을 보실 수 있습니다.

{% include image.html
file='deep-learning-aws-cuda-install_1.png'
%}
<br>

Install을 선택한 후 한참을 기다리면 다음과 같은 메시지를 볼 수 있습니다.

{% include image.html
file='deep-learning-aws-cuda-install_2.png'
%}
<br>

설치가 잘 진행됬으면 `PATH`를 설정해야 합니다.
<br><br>

기본적으로 ubuntu는 전역변수 `/etc/bash.bashrc`에 설정하면 되며, 사용자 마다 각기 다른 CUDA를 사용하게 될 경우에는
사용자 home폴더에 .bashrc를 수정해 주면 됩니다.
<br><br>

우리는 그냥 `/etc/bash.bashrc`를 수정하기로 하죠.
<br><br>

파일을 vi로 열고 아래의 내용을 파일의 마지막에 추가합니다.
<br><br>

> `sudo vi /etc/bash.bashrc`

~~~text

##### ADD #####
export PATH=/usr/local/cuda-11.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64:$LD_LIBRARY_PATH
################

~~~

다음의 명령을 이용해 설정한 PATH를 현재 터미널에 적용합니다.
<br><br>

> `source /etc/bash.bashrc`

<br>

이제 설치가 잘 되었는지 CUDA version을 확인해 보죠.
<br><br>

> `nvcc -V`

~~~text

nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2021 NVIDIA Corporation
Built on Sun_Aug_15_21:14:11_PDT_2021
Cuda compilation tools, release 11.4, V11.4.120
Build cuda_11.4.r11.4/compiler.30300941_0

~~~

NVIDIA CUDA 11.4 버전이 잘 설치된것을 확인할 수 있습니다.
<br><br>

이제 `cuDNN 8.2.2`를 설치할 차례입니다.
<br><br>

[cuDNN download](https://developer.nvidia.com/rdp/cudnn-download){: target="_blank" }
<br><br>

NVIDIA site에 로그인 한 후 현재 설치되어 있는 CUDA 버전에 맞는 버전을 찾아 다운로드 합니다.
추후에 `Tensorflow Object Detection API` 같은걸 사용하기 위해서는 최소 `8.1` 버전 이상을
설치해야 합니다.
<br><br>

저 같은 경우 `Download cuDNN v8.2.2 (July 6th, 2021), for CUDA 11.4` 을 클릭해
그 안의 `cuDNN Library for Linux (x86_64)`를 선택해 tar 파일을 다운로드 받았습니다.
<br><br>

tar xvf 명령을 이용해 압축을 풀면 `cuda`라는 폴더가 생성됩니다.
<br><br>

폴더에 진입하게 되면 `include` 폴더와 `lib64` 폴더 및 txt파일이 하나 있습니다.
include 폴더안의 파일은 cuda의 include로 lib64 폴더안의 파일은 cuda의 lib64로 복사해 주면 됩니다.
<br><br>

다음과 같은 명령어를 이용하시면 됩니다.
<br><br>

> `sudo cp include/cudnn* /usr/local/cuda-11.4/include`

> `sudo cp lib64/libcudnn* /usr/local/cuda-11.4/lib64/`

> `sudo chmod a+r /usr/local/cuda-11.4/lib64/libcudnn*`

<br>

이제 마지막으로 잘 설치되었는지 확인해보겠습니다.

~~~text

cat /usr/local/cuda-11.4/include/cudnn_version.h | grep CUDNN_MAJOR -A 2

결과내용으로 다음과 같은 내용이 나오면 됩니다. 

#define CUDNN_MAJOR 8
#define CUDNN_MINOR 2
#define CUDNN_PATCHLEVEL 2

~~~

`cuDNN 8.2.2` 버전이 정상적으로 설치된걸 확인할 수 있습니다.

End.

{% include links.html %}
