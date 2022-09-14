---
title: NumPy Introduction
sidebar: numpy_sidebar
summary: "데이터 과학에 대한 설명과 NumPy의 기본적인 개념입니다."
permalink: numpy_introduction.html
folder: numpy
---

## Data Science

바야흐로 `Big Data`의 시대입니다. 현재 많은 국가들이 `AI`와 `Big Data` 기술을
굉장히 빠른 속도로 발전시키며 우리 삶의 패러다임을 변경시키고 있습니다.
<br><br>

과거와는 달리 엄청나게 빠른 속도로 쌓여가는 방대한 데이터와 그 데이터를 저장, 분석할
수 있는 Computing Power의 결합이 `Data Science`라는 영역을 크게 확대시켰습니다.
<br><br>

과거 이 분야는 일반인이 접근하기 쉽지 않은 분야였는데 `Cloud Computing`이 확산되면서
일반인들도 `Big Data`를 저장하고 분석하는 `Computing Resource`를 저렴한 비용으로
사용가능하게 되었습니다. 이러한 클라우드 컴퓨팅은 앞으로도 지속적으로 발전해 나가면서
더 저렴한 가격으로 여러가지 다양한 서비스들을 우리에게 제공해 줄 것입니다.
<br><br>

이런 `Computing Power`의 대중화는 많은 사람들에게 최적의 학습 환경과 인프라를
제공하지만 분석 대상인 `Data`가 없으면 이런 인프라도 의미가 없습니다.
<br><br>

가장 중요한 자원은 `Data`입니다.
<br><br>

실제로 데이터 분석 업무는 80% 이상이 데이터를 수집하고 가공하는 일이 차지하고
있습니다. 나머지 20% 정도가 분석기법을 적용해 그 결과를 분석해서 우리에게
유용한 정보(Information)을 뽑아내는 분석 프로세스입니다.
<br><br>

따라서 우리에게 가장 필요한 능력 중 하나는 데이터를 수집하고 분석이
가능한 형태로 가공, 정리하는 것이라 할 수 있습니다.
<br><br>

앞으로 우리가 학습하게 되는 `Pandas`는 Data를 수집, 가공, 정리하는데 최적화된
library라고 할 수 있습니다. 단적으로 말하면 `Pandas`를 이용하면 데이터 분석의
초기업무 80% 정도는 할 수 있다는 말이 되는 것입니다.
<br><br>

이런 `Pandas`를 배우기에 앞서 이 `Pandas`의 기본 자료구조를 구성하고 있는
`Numpy`에 대해서 배우고 그 이후에 `Pandas` 데이터 분석에 대해서
알아보도록 하겠습니다.

---

## NumPy

`NumPy`(Numerical Python)에 대해서 알아보겠습니다.
<br><br>

`NumPy`는 대규모 다차원 배열과 행렬 연산에 있어 상당한 편의성을 제공하는 python module입니다.
또한 `Pandas`와 `Matplotlib`의 기반이 되는 module이며 Machine Learning, Deep Learning
에서 기본 자료구조로 사용되기 때문에 잘 알아두어야 합니다.
<br><br>

NumPy는 `ndarray`라고 부르는 n차원의 배열(n-dimensional array)객체를 사용합니다.
<br><br>

ndarray는 Python의 list와는 다르게 같은 데이터 타입만 저장 가능합니다. NumPy의 ndarray와
Python의 list는 거의 사용방법이 같지만 NumPy ndarray가 좀 더 효율적으로 메모리에
데이터를 저장하고 빠른 연산이 가능합니다. 하지만 ndarray는 차원의 개념을 가지고 있고 연산방식이
Python의 list와 다르기 때문에 이 부분에 주의해야 합니다.
<br><br>

NumPy를 사용하기 위해서는 conda 명령어나 pip를 이용하여 먼저 NumPy module을 설치해야 합니다.

{% include callout.html
type="danger"
content="**conda install numpy**"
%}


End.

{% include links.html %}
