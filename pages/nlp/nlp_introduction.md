---
title: Natural Language Processing Introduction
sidebar: nlp_sidebar
summary: "NLP Introduction입니다."
permalink: nlp_introduction.html
folder: nlp
---

## Sequential Data

이번에는 RNN에 대해서 알아보도록 하겠습니다. `RNN`에 대해서 이야기하기 앞서 먼저 알아야 되는
내용은 바로 `Sequential Data`(순차데이터)입니다.
<br><br>

순차데이터는 텍스트나 일정 시간 간격으로 기록된 데이터인 `time series data`(시계열 데이터)와
같이 순서에 의미가 있는 데이터를 의미합니다.
<br><br>

아주 간단한 예로 영어 문장을 생각해 볼 수 있습니다. `I am a boy`는 쉽게 이해할 수 있지만
`boy a I am`은 말이 되지 않죠. 또한 일별 온도같은 데이터에 대해 날짜를 뒤죽박죽으로 섞어 놓는다면
내일의 온도를 쉽게 예상하기 어렵겠죠. 이러한 순서에 대한 중요성이 부여된 데이터를 순차데이터라고
합니다.
<br><br>

`CNN`은 데이터 순서와는 상관이 없었습니다. 심지어 training data set을 사용할 때
데이터를 골고루 섞는 편이 결과가 더 좋은 경우도 있습니다. `CNN`뿐만 아니라 일반적인 머신러닝 모델에서도
마찬가지 입니다. 어떤 샘플이 먼저 주어져도 모델의 학습에는 큰 영향을 미치지 않습니다.
<br><br>

하지만 가장 대표적인 예인 `댓글`과 같은 텍스트 데이터는 단어의 순서가 상당히 중요한 순차 데이터입니다.
이런 텍스트는 순서를 유지하며 신경망에 주입해야 합니다. 단어의 순서를 섞어서 주입하면 다른 의미가 될
수 있기 때문이죠.
<br><br>

> 따라서 순차 데이터를 다룰 때는 기본적으로 이전에 입력한 데이터를 기억하는 기능이 필요합니다.

<br>

`Fully Connected Neural Network`(완전 연결 신경망)이나 `CNN`(합성곱 신경망)은
이런 기억 기능이 없습니다. 하나의 샘플을 사용하여 Feed Forward 계산을 수행하고 나면 해당 샘플은
버려지게 됩니다. 기존에 배웠던 신경망들은 모두 입력데이터의 흐름이 앞으로만 전달되는
`Feed Forward Neural Network`이었던 것이죠.
<br><br>

신경망이 이전에 처리했던 샘플을 다음 샘플을 처리하는데 재사용하기 위해서는 데이터 흐름이 앞으로만
전달되어서는 곤란하겠죠? 다음 샘플을 위해서 이전 데이터가 신경망 층에 순환될 필요가 있는데 이런
신경망을 `Recurrent Neural Network`(순환 신경망, RNN)이라고 합니다.
<br><br>

인공신경망의 분류를 간단하게 그림으로 보자면 다음과 같습니다.

{% include image.html
file='deep-learning-neural-network-category.png'
%}
<br>

---

## RNN의 Concept

`RNN`(순환 신경망)은 일반적인 완전 연결 신경망과 거의 비슷합니다. 아래 그림과 같이
완전 연결 신경망에 이전 데이터를 처리하는 부분만 추가하면 되니까 말이죠.

{% include image.html
file='deep-learning-rnn-simple-concept-figure.png'
%}
<br>

그림에서 알 수 있듯이 `neuron`의 출력이 다시 자기 자신으로 전달됩니다. 즉, 어떤 샘플을
처리할 때 바로 이전에 사용했던 데이터를 재사용하는 것이죠.
<br><br>

아주 간단한 예를 그림으로 알아보죠.
<br><br>

다음그림처럼 하나의 샘플안에 있는 3개의 데이터를 처리하는 순환 신경망의 neuron이 있다고 가정합니다. `O`는
출력된 결과입니다. 첫번째 샘플 A를 처리하고 난 출력 O<sub>A</sub>이 다시 뉴런으로 들어갑니다.

{% include image.html
file='deep-learning-rnn-mechanism-1.png'
%}
<br>

그 다음 B를 처리할 때 앞에서 A를 사용해 만든 출력 O<sub>A</sub>를 함께 사용합니다.

{% include image.html
file='deep-learning-rnn-mechanism-2.png'
%}
<br>

위에서 만든 O<sub>B</sub>에는 당연히 A에 대한 정보가 어느 정도 포함되어 있겠죠? 그 다음
C를 처리할 때는 O<sub>B</sub>를 함께 사용합니다.

{% include image.html
file='deep-learning-rnn-mechanism-3.png'
%}
<br>

당연한 말이지만 O<sub>C</sub>에는 O<sub>B</sub>를 사용했으므로 B에 대한 정보가 어느 정도
포함되어 있을 것입니다. 또 O<sub>B</sub>에는 A에 대한 정보도 포함되어 있습니다. 따라서
O<sub>C</sub>에는 B와 A에 대한 정보가 담겨 있다고 할 수 있습니다.
<br><br>

물론 O<sub>C</sub>에는 A에 대한 정보보다는 B에 대한 정보가 더 많은건 당연합니다. 이렇게
샘플을 처리하는 한 단계를 `time step`(타임스텝)이라고 합니다.
<br><br>

위의 예에서도 언급했지만 이 `RNN`은 이전 `time step`의 샘플을 기억하지만 time step이
오래될수록 순환되는 정보는 희미해지게 됩니다.
<br><br>

`RNN`에서는 `layer`나 `neuron`을 `cell`이라고 부릅니다.
일반적으로 RNN의 순환층에는 여러 개의 neuron(cell)이 존재하지만
그림으로 그릴 때 완전 연결 신경망과 달리 모든 cell을 표시하지 않고 하나의 cell로 layer를
표현합니다. 그래서 layer를 cell이라고 하기도 하는 것입니다.
또한 cell의 출력을 `hidden state`(은닉 상태)라고 합니다.
<br><br>

`CNN`과 구조는 거의 같습니다. 하지만 부르는 이름이 조금씩 차이가 나게 됩니다.
그림으로 표현하면 다음과 같이 표현할 수 있습니다.

{% include image.html
file='deep-learning-rnn-cell-structure.png'
%}
<br>

일반적으로 활성화 함수는 `tanh`(하이퍼볼릭 탄젠트)를 사용합니다. `tanh`는 `sigmoid`처럼
S자 형태의 함수인데 `sigmoid`는 0 ~ 1 사이의 값을 가지는 반면 `tanh`는 -1 ~ 1 사이의
값을 가집니다. 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='deep-learning-rnn-hyperbolic-tangent.png'
%}
<br>

순환 신경망의 neuron(cell)은 가중치가 하나 더 있습니다. 바로 이전
time step의 `hidden state`(은닉상태) 에 곱해지는 가중치입니다. `cell`은 입력과
이전 time step의 hidden state를 사용하여 현재 time step의 hidden state를 만들게 됩니다.
<br><br>

아래의 그림에서 2개의 가중치를 cell안에 표현해 놨습니다. w<sub>x</sub>는 입력에 곱해지는
가중치이고 w<sub>h</sub>는 이전 time step의 hidden state에 곱해지는 가중치입니다. 당연히
neuron(cell)마다 하나의 `bias`(절편)이 존재하는것은 동일합니다. 그림의 왼쪽을 time step으로
펼치면 오른쪽 그림이 됩니다. 그림에서 알 수 있듯이 모든 time step에서 사용되는
가중치 w<sub>h</sub>는 하나입니다. 이 가중치 w<sub>h</sub>는 time step에 따라
변화되는 neuron(cell)의 출력을 학습하게 됩니다.
<br><br>

그림에서 맨 처음 timestep에서 사용된 hidden state(h<sub>0</sub>)는 존재하지 않기 때문에 0으로
초기화해서 사용합니다.

{% include image.html
file='deep-learning-rnn-spread-timestep.png'
%}
<br>

그럼 지금까지의 내용을 가지고 순환신경망의 cell에서 필요한 가중치의 개수를 알아보도록
하겠습니다.
<br><br>

아래 그림처럼 순환층에 입력되는 특성의 개수가 4개이고 순환층의 neuron(cell)이 3개인 경우를 예로
들어보죠.

{% include image.html
file='deep-learning-rnn-weight-count-example.png'
%}
<br>

먼저 w<sub>x</sub>의 크기를 구해보죠. 입력층과 순환층이 모두 완전 연결되어 있기 때문에
가중치 w<sub>x</sub>의 개수는 `4 * 3 = 12`개가 됩니다.
<br><br>

순환층에서 다음 time step에 사용되는 hidden state를 위한 가중치 w<sub>h</sub>는 어떻게
될까요? 그림을 먼저 보겠습니다.

{% include image.html
file='deep-learning-rnn-cell-weight-count-example.png'
%}
<br>

순환층에 있는 첫번째 neuron `r1`의 hidden state가 다음 time step에 재사용될 때 첫번째
neuron과 두번째, 세번째 neuron에 모두 전달되는것에 주의해야 합니다. 즉, 이전 time step의
hidden state는 다음 time step의 neuron에 완전 연결됩니다. 따라서 그림으로 그려보면
위의 그림처럼 그려지는 것이죠. 결국 이 순환층에서 hidden state를 위한 가중치 w<sub>h</sub>
의 개수는 `3 * 3 = 9`개입니다.
<br><br>

가중치는 모두 구했고 이제 각 neuron마다 하나씩의 bias가 존재하니 이 순환층은 모두
`12 + 9 + 3 = 24`개의 model parameter를 가지게 됩니다.
<br><br>

자 그러면 이제 우리가 알고 있는 DNN과 RNN의 아키텍처를 비교해서 보도록 하겠습니다.

{% include image.html
file='deep-learning-dnn-architecture-simple.png'
%}

{% include image.html
file='deep-learning-rnn-architecture.png'
%}
<br>

RNN은 위의 그림에서 알 수 있듯이 크게 2가지 부분에서 기존 DNN과 다릅니다.

* activation function을 `relu`가 아닌 `tanh`를 이용합니다.

* Hidden Layer에 순환구조를 가지고 있습니다. `tanh`의 계산결과가 이후 결과계산에
  영향을 미치도록 Hidden Layer안에서 순환하는 구조를 가지고 있습니다.

<br>

---

## RNN의 입력과 출력

이번에는 순환층의 입력과 출력에 대해서 알아보도록 하겠습니다. `CNN`에서 입력은
전형적으로 하나의 샘플(이미지)이 3차원을 가지게 됩니다. 높이, 너비, channel이 그것이죠.
<br><br>

입력이 convolution layer와 pooling layer를 통과하면 높이, 너비, channel의 크기가 달라지지만
차원의 개수는 당연히 3차원이 유지됩니다.
<br><br>

순환층은 일반적으로 샘플마다 2개의 차원을 가집니다. 하나의 샘플을 하나의 `sequence`라고 말합니다.
이 `sequence`안에는 여러 개의 아이템들이 들어있습니다. 여기에서 `sequence`의 길이가 바로
`time step`의 길이가 됩니다.
<br><br>

예를 들어, 어떤 샘플에 `I am a boy`라는 문장이 들어 있다고 가정해보죠. 이 샘플은 4개의 단어로
이루어져 있습니다. 각 단어를 3개의 어떤 숫자로 표현한다고 가정하면 다음과 같은 그림이 됩니다.

{% include image.html
file='deep-learning-rnn-text-sample-example.png'
%}
<br>

이런 입력이 순환층을 통과하면 두번째, 세번째 차원이 사라지고 순환층의 neuron 개수만큼
출력이 됩니다. 하나의 샘플은 sequence 길이(단어 개수)와 숫자로 표현한 단어 표현의
2차원 배열이 됩니다. 이 2차원 배열이 순환층을 통과하면 1차원 배열로 바뀌게 된다는 것이죠.
이 1차원 배열의 크기는 순환층의 neuron 개수에 의해 결정됩니다.
<br><br>

그림으로 나타내면 다음과 같습니다.

{% include image.html
file='deep-learning-rnn-recurrent-layer-through.png'
%}
<br>

왜 이렇게 될까요? 여기서 약간 주의해야 할 점이 있는데 위에서 cell의 출력을 설명할 때
마치 모든 time step에서 출력을 만든 것처럼 설명했었습니다. 하지만 사실 순환층은
기본적으로 마지막 time step의 hidden state만 출력으로 내보냅니다.
<br><br>

그림으로 표현하면 다음처럼 되겠죠. 여기서 h<sub>f</sub>는 마지막 time step의 hidden state가
됩니다.

{% include image.html
file='deep-learning-rnn-recurrent-layer-output.png'
%}

생각해보면 입력된 sequence 길이를 모두 읽어서 정보를 마지막 hidden state에 압축하여 전달하는
것으로 생각할 수 있습니다.
<br><br>

순환신경망도 완전연결신경망이나 `CNN`처럼 여러 개의 층을 쌓을 수 있습니다. 여기서 살짝 주의해야
하는데 순환층을 여러 개 쌓았을 때는 cell의 출력에 조심해야 합니다. 앞서 언급했듯이 cell의
입력은 샘플마다 time step과 단어표현의 2차원 배열이어야 합니다. 따라서 첫번재 cell이 마지막
time step의 hidden state만 출력해서는 안됩니다. 이런 경우 마지막 cell을 제외한 다른
모든 cell은 모든 time step의 hidden state를 출력해야 합니다.
<br><br>

다음 그림은 2개의 순환층을 쌓은 경우입니다.

{% include image.html
file='deep-learning-rnn-recurrent-layer-twice.png'
%}
<br>

마지막으로 출력층의 구조도 알아보도록 하죠. `CNN`과 마찬가지로 `RNN` 역시 마지막에 `dense`층을
두어 class를 분류합니다. 다중 분류인 경우 output layer에 class의 개수만큼 neuron을 배치하고
softmax를 이용하여 분류하게 됩니다.
<br><br>

`CNN`과 다른 점은 마지막 셀의 출력이 1차원이기 때문에 `Flatten`을 해 줄 필요가 없다는 것입니다.
cell의 출력을 그대로 `dense`에 사용할 수 있습니다.
<br><br>

`RNN`은 `CNN`보다 이해하기가 더 어렵기 때문에 일단 간단한 내용을 가지고 전체적인 구조부터 한번
살펴보았습니다. 거시적인 구조는 다른 신경망과 크게 다르지 않습니다. 단, 순환층에서 이전 time step의
출력을 다음 time step의 입력으로 사용한다는 점, 그리고 순환층을 중첩해서 사용하는 경우를
제외하고는 마지막 time step의 출력이 다음 layer로 전달된다는 점만 신경쓰시면 될 듯 합니다.

End.

{% include links.html %}
