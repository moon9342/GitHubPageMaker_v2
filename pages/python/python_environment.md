---
title: Python Environment
sidebar: python_sidebar
summary: "많이 사용되어지는 python 개발환경에 대해 알아보겠습니다."
permalink: python_environment.html
folder: python
---

## 기본적인 Python 개발환경

기본적인 Python 개발환경부터 알아보겠습니다.
<br><br>

먼저 [Python Official Homepage](http://www.python.org/){:target="_blank"}에 접속해 다운로드 페이지에서
Python을 다운로드 합니다. `Python 3.8`버전을 다운로드 합니다.
(추후에 사용할 Google의 `Tensorflow`는 Python version에 dependent합니다.)
또한 언어 특성상 Python 2.x버전과 3.x버전은 차이가 있습니다. 2.x 버전으로는 이제 사용하지 않습니다.
<br><br>

다운로드가 끝나면 기본 형태로 설치를 진행합니다. `PATH` 추가하는 부분은 포함시켜 설치를 진행해야 사용하기 편합니다.
<br><br>

이제 사용할 `IDE`(Integrated Development Environment)를 설치합니다. 일반적으로
`PyCharm`을 많이 이용합니다. 

`Community edition`과 `Professional edition`이 있습니다.
Professional Edition은 30일 trial version으로 사용할 수 있습니다. 그 이후에는 비용을 지불해야 합니다.
기능면으로는 당연히 `Professional Edition`이 좋고 추후에 `Jupyter Notebook`을 PyCharm에서 사용할 수 있도록
`Professional Edition`을 사용합니다.(교육용 license를 이용하면 1년 무료로 이용할 수 있습니다.)
<br><br>

또한 참고로 다음의 링크를 이용하면 4개월 무료 license를 받을 수 있습니다. 단, 이벤트 성이기 때문에
언제 종료될지는 알 수 없습니다.
<br><br>

[PyCharm Professional Edition 4개월 무료 license](https://www.jetbrains.com/pycharm/promo/test-and-code/){:target="_blank"}
<br><br>

링크를 타고 들어가면 PyCharm Professional Edition 4개월 무료 이용할 수 있는 쿠폰 코드가 있습니다.
복사한 후 `Get PyCharm 4-months free` 버튼을 눌러서 `PyCharm buy now` 버튼을 누르고 진행하면
쿠폰 코드를 입력하는 부분이 있는데, 거기에 코드를 입력하면 4월 무료 이용으로 바뀌게됩니다. 0원으로 결제하면,
가입한 이메일 계정으로 이메일이 옵니다.
<br><br>

[JetBrains](https://www.jetbrains.com/){:target="_blank"}사이트로 접속한 후
[PyCharm](https://www.jetbrains.com/pycharm){:target="_blank"}을 다운로드 받습니다.

다운로드가 완료되면 기본형태로 설치합니다. 설치가 끝나면 프로젝트를 생성할 수 있는
화면이 나오게 됩니다.

---

## Anaconda Jupyter Notebook 환경

Data Analysis를 위해서 Python을 학습하고 사용할때는 `Jupyter Notebook` 또는 `Google CoLab`을
많이 이용합니다. 먼저 Anaconda를 이용해 Jupyter Notebook을 PC에 설치해서 사용하는 법에 대해 알아보고 추후에 Google CoLab에 대해서도
알아보도록 하겠습니다.
<br><br>

참고로 `Anaconda`는 Continuum Analytics에서 개발한 Python Data Science Platform 중 가장
유명한 Platform입니다. 가상환경 생성 및 데이터 분석에 필요한 여러 라이브러리들을 편하게 설치, 사용할 수 있는 환경을 제공합니다.
<br><br>

또한 `Jupyter Notebook`은 Python, R 파일을 작성하고 실행하는 개발환경을 제공하는 Web 기반의 개발도구입니다.
`Cell`이라는 단위로 코드를 나누어 실행 할 수 있기 때문에 `interactive`한 개발이 가능하다는 것이 가장 큰 장점입니다.
<br><br>

* `Anaconda` 다운로드 및 설치
* Anaconda는 무료로 사용가능하며 python과 다수의 유용한 package를 제공하고 있습니다.
* Anaconda 설치 후, Anaconda Prompt를 관리자 권한으로 실행한 후 pip와 Anaconda
  파이썬 패키지를 최신 버전으로 upgrade 합니다.
* `python -m pip install --upgrade pip`
* `conda update -n base conda`
* `conda update --all`
* conda 가상 환경을 생성합니다.
* `conda create  -n data_env python=3.8 openssl`
* 현시점(2022-10-24)을 기준으로 nb_conda 설치를 위해서는 python버전이 `3.8`버전이어야 합니다. 3.9버전일 경우
오류가 발생합니다.
* 가상환경으로 전환하기 위해서 다음의 코드를 실행
* `conda activate data_env`
* 필요한 주요 라이브러리를 설치하기 위해 다음과 같이 실행합니다.(이 부분은 해당 내용을 학습할 때 설치해도 됩니다.) 
* `conda install numpy pandas matplotlib`
* `conda install -c conda-forge scikit-learn`
* `conda install -c conda-forge tensorflow`
* jupyter notebook에서는 여러 버전의 python과 여러 개의 conda 가상환경을 구동할 수 있습니다.
  하지만 기본 설정으로는 실행되지 않고 `nb_conda`를 설치해야 Jupyter Notebook이 설치됩니다.
* `conda install nb_conda`
* jupyter notebook 설정 파일을 생성하기 위해 Anaconda prompt에서 `jupyter notebook --generate-config` 실행
  생성된 설정파일을 수정하여 실습에서 사용할 폴더를 `C:/jupyter_notebook`로 설정한 후 해당 폴더를 생성합니다.
* 실행결과 : Writing default config to: C:/Users/shmoon/.jupyter/jupyter_notebook_config.py
* 해당파일내에서 다음의 코드를 찾아 다음과 같이 수정하고 해당 라인의 주석을 해제합니다.(폴더는 수동으로 생성해야 합니다.)
* `c.NotebookApp.notebook_dir = 'C:/jupyter_notebook'`
* notebook 실행 ( `jupyter notebook` )
<br><br>

가상환경 listing 및 삭제는 다음과 같이 수행합니다. 단, 삭제시 폴더는 수동으로 모두 삭제해야 합니다.

~~~python

conda info --envs

conda remove -n data_env --all

~~~

---

## PyCharm과 Anaconda Jupyter Notebook 연동

Jupyter Notebook은 정말 편하게 Machine Learning 개발을 진행할 수 있도록 도와주지만
Web Application이라는 한계때문에 기존 개발 IDE에서 기본으로 제공되는 `Assist`와 같은
기능을 원할하게 사용하지 못한다는 단점이 있습니다. Assist가 안되는건 아닌데 사용하기가 많이
불편합니다.
<br><br>

이 문제는 `PyCharm`을 이용하면 해결할 수 있습니다. PyCharm에서 Jupyter Notebook 파일을 생성해서
사용할 수 있고 사용하는 Kernel을 Anaconda에서 설치한 Jupyter Notebook의 Kernel을 이용할 수 있습니다.
혹은 아예 가상환경을 새로 생성해서 사용할 수 있습니다.
<br><br>

Anaconda Prompt에서 Jupyter Notebook을 실행시키면 token 키가 포함된 URL을 제공해 주는데 이를 PyCharm의 환경설정에
넣어주면 쉽게 PyCharm에서 Jupyter Notebook Kernel을 연동할 수 있습니다. 아래 그림처럼 말이죠.
<br><br>

단, 이 기능은 PyCharm Community Edition은 제공하지 않고 `Professional Edition`만 제공합니다.

{% include image.html
file='pycharm-jupyter-config.png'
%}
<br>

{% include image.html
file='pycharm-jupyter-screenshot.png'
%}
<br>

End.

{% include links.html %}
