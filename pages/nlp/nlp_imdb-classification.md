---
title: Natural Language Processing IMDB 분류
sidebar: nlp_sidebar
summary: "IMDB Review 분류 구현입니다."
permalink: nlp_imdb-classification.html
folder: nlp
---

## IMDB Review 분류

앞서 RNN의 기본적인 개념을 배웠으니 이번에는 대표적인 RNN 문제 중 하나인 `IMDB Review Data Set`을
사용해 간단한 순환 신경망 모델을 구현해 보겠습니다.
<br><br>

이 문제에서 주의해야 할 점은 데이터셋의 변형입니다. 두 가지 방법으로 데이터를 변형하여 신경망에
주입할 계획인데 하나는 우리에게 익숙한 `one-hot encoding` 방식으로 데이터를 변형할 것이고 다른
하나는 `Embedding`입니다. 먼저 상대적으로 익숙한 `one-hot encoding` 방식으로 데이터를 변형시켜서
훈련해보도록 하죠.
<br><br>

사용할 데이터셋인 `IMDB Review Data Set`은 유명한 인터넷 영화 데이터베이스인 `imdb.com`에서
수집한 review를 감상평에 따라 긍정과 부정으로 분류해 놓은 데이터셋입니다. 총 50,000개의 샘플로
이루어져 있고 train data와 test data에 각각 25,000개씩 나누어져 있습니다.
<br><br>

우리가 하려는 작업은 IMBD Review를 감상평에 따라서 분류하는 감성 분석 작업입니다. 일반적으로
`NLP`(Natural Language Processing)는 컴퓨터를 사용해 인간의 언어를 처리하는 분야를 말하는데
대표적인 응용으로 음성 인식, 기계 번역, 감정 분석등이 있습니다. 이런 자연어 처리에서 훈련 데이터를
종종 `corpus`(말뭉치)라고 부릅니다. 즉, 우리의 IMDB Review Data Set은 하나의 corpus(말뭉치)
입니다.
<br><br>

이전 이미지를 대상으로 했을 때는 이미지 자체가 숫자 데이터로 되어 있기 때문에 데이터를 특별하게
변환시키지는 않았습니다. 물론 정규화 같은 작업은 진행했지만 기본적으로 숫자이기 때문에 약간의
변경을 가하는 정도였죠. 그런데 지금 처리해야 할 데이터는 `Text` 데이터 입니다.
<br><br>

당연한 말이지만 텍스트 자체를 신경망에 주입하지는 않습니다. 컴퓨터에서 처리하는 모든 데이터는
당연히 숫자 데이터입니다. 따라서 텍스트 데이터의 경우 단어를 숫자 데이터로 바꾸는 방법이 필요한데
가장 일반적인 방법은

> 데이터에 등장하는 단어마다 고유한 정수를 부여하는 것입니다.

<br>

예를 들면 아래 그림처럼 말이죠.

{% include image.html
file='deep-learning-imdb-text-to-digit.png'
%}
<br>

문장에 등장하는 각 단어를 하나의 정수에 매핑했고, 동일한 단어는 당연히 동일한 정수에
매핑됩니다. 일반적으로 영어는 모두 소문자로 바꾸고 구둣점(period)를 제거한 뒤 공백을
기준으로 분리합니다. 이렇게 분리된 단어를 `token`(토큰)이라고 부릅니다. 하나의 샘플은
여러개의 token으로 이루어져 있고 1개의 token이 하나의 time step에 해당하게 되겠죠.
<br><br>

참고로 영어는 비교적 간단히 토큰을 분리할 수 있는데 한글은 이 문제가 좀 많이 까다롭습니다.
한글은 조사가 발달되어 있기 때문이죠. 단순히 공백으로 분리해서 token을 생성할 수 없습니다.
한글은 형태소 분석이라는 작업을 거쳐야 하는데 상당히 복잡한 내용이기 때문에 우리가 직접
작업할 수는 없고 `KoNLPy`라는 모듈을 이용해서 처리하게 됩니다.
<br><br>

여하간 training set에서 고유한 단어를 뽑아 만든 목록을 우리는 `Vocabulary`
(어휘 사전, 단어 사전)이라고 합니다. token에 할당하는 정수 중 몇개는 특정한 용도로
예약되어 있는 경우가 많습니다. 예를 들면

* `0`같은 경우 : `padding`
* `1`같은 경우 : 문장의 시작
* `2`같은 경우 : 어휘 사전에 없는 token

<br>

이런 식입니다.
<br><br>

원래 IMDB Review Data는 영어로 되어 있는 Text 문장이지만 tensorflow keras안에 이미 정수로
바꾼 데이터가 포함되어 있습니다. 해당 module을 이용해서 데이터를 적재해 보도록 하죠. 여기서는
가장 많이 등장하는 단어 500개만 사용하여 데이터를 적재하도록 하겠습니다.
(실습은 AWS EC2에서 진행하거나 Google Colab에서 진행하시면 됩니다.)

~~~python

from tensorflow.keras.datasets import imdb

(x_data_train, t_data_train), (x_data_test, t_data_test) = \
imdb.load_data(num_words=500)

~~~

데이터 적제가 끝나면 이제 데이터가 어떻게 구성되어 있는지 살펴보죠.

~~~python

print(x_data_train.shape, t_data_train.shape)   # (25000,) (25000,)
print(x_data_test.shape, t_data_test.shape)   # (25000,) (25000,)

print(type(x_data_train))  # <class 'numpy.ndarray'>
print(type(x_data_train[0])) # <class 'list'>

print(x_data_train[0])
# [1, 14, 22, 16, 43, 2, 2, 2, 2, 65, 458, 2, 66, 2, 4, 173, 36, 256, 5, 
# 25, 100, 43, 2, 112, 50, 2, 2, 9, 35, 480, 284, 5, 150, 4, 172, 112, 167, 
# 2, 336, 385, 39, 4, 172, 2, 2, 17, 2, 38, 13, 447, 4, 192, 50, 16, 6, 147, 
# 2, 19, 14, 22, 4, 2, 2, 469, 4, 22, 71, 87, 12, 16, 43, 2, 38, 76, 15, 13, 
# 2, 4, 22, 17, 2, 17, 12, 16, 2, 18, 2, 5, 62, 386, 12, 8, 316, 8, 106, 5, 4, 
# 2, 2, 16, 480, 66, 2, 33, 4, 130, 12, 16, 38, 2, 5, 25, 124, 51, 36, 135, 48, 
# 25, 2, 33, 6, 22, 12, 215, 28, 77, 52, 5, 14, 407, 16, 82, 2, 8, 4, 107, 117, 
# 2, 15, 256, 4, 2, 7, 2, 5, 2, 36, 71, 43, 2, 476, 26, 400, 317, 46, 7, 4, 2, 2, 
# 13, 104, 88, 4, 381, 15, 297, 98, 32, 2, 56, 26, 141, 6, 194, 2, 18, 4, 226, 22, 
# 21, 134, 476, 26, 480, 5, 144, 30, 2, 18, 51, 36, 28, 224, 92, 25, 104, 4, 226, 
# 65, 16, 38, 2, 88, 12, 16, 283, 5, 16, 2, 113, 103, 32, 15, 16, 2, 19, 178, 32]

print(len(x_data_train[0])) # 218 => 첫번째 Review의 길이(218개의 token)
print(len(x_data_train[1])) # 189 => 두번째 Review의 길이(189개의 token)

print(t_data_train[0:10]) # [1 0 0 1 0 0 1 0 1 0]   긍정(1) 부정(0)

~~~

train 데이터와 test 데이터가 각각 25,000개의 샘플로 이루어져 있습니다. 그런데
x_data_train의 shape을 살펴보면 1차원 ndarray입니다. 즉, 요소 1개가 1개의
Review를 담고 있다는 것인데 이떻게 담겨있는지를 확인해 보면 python list로
표현되어 있는것을 확인할 수 있습니다.
<br><br>

다음 그림처럼 데이터가 담겨 있다는 의미입니다. 여기서 train_input은 x_data_train을
의미합니다.

{% include image.html
file='deep-learning-imdb-ndarray-structure.png'
%}
<br>

왜 이렇게 데이터를 담아놨을까요? IMDB Review의 텍스트 길이는 당연히 제각각입니다. 따라서
2차원 고정 배열을 사용하게 되면 공간의 낭비가 심하게 될 여지가 있습니다. 그래서
공간의 낭비를 줄이기 위해 하나의 Review에 대한 내용을 python list에 담아
numpy array에 넣어놓은 것 입니다.
<br><br>

t_data_train을 출력해보면 해당 Review가 긍정(1)인지 부정(0)에 대한 정보가 들어있는 것을
확인할 수 있습니다.
<br><br>

`padding`의 개념을 알아보기 위해 각 Review의 길이에 대해서 알아보도록 하죠.

~~~python

import numpy as np
import matplotlib.pyplot as plt

# 각 Review의 길이를 알아내서 ndarray에 저장
lengths = np.array([len(x) for x in x_data_train])
print(lengths[:10])

# Review 길이의 평균과 중간값을 출력
print(np.mean(lengths), np.median(lengths))
# 238.71364 178.0

# 평균은 239, 중간값은 178이기 때문에 Review 길이 데이터는 한쪽으로
# 치우친 데이터임.

plt.hist(lengths)
plt.show()

~~~

{% include image.html
file='deep-learning-imdb-review-histogram.png'
%}
<br>

위의 histogram을 보면 대부분의 Review 길이는 300 미만입니다. 그런데 사실 Review는
대부분 짧아서 우리는 중간값보다 훨씬 작은 100개의 단어만을 사용하도록 하겠습니다.
<br><br>

이때 여전히 100개의 단어보다 작은 Review가 있습니다. 이런 Review들의 길이를 100에
맞추기 위해 `padding`이 필요합니다. 보통 padding을 나타내는 token으로는 `0`을 사용합니다.
<br><br>

이 작업을 위해 keras는 우리에게 적절한 함수를 하나 제공하고 있습니다. 아래의 코드를 보시죠.

~~~python

from tensorflow.keras.preprocessing.sequence import pad_sequences

x_data_train_seq = pad_sequences(x_data_train, maxlen=100)

print(type(x_data_train_seq), x_data_train_seq.shape) 
# <class 'numpy.ndarray'> (25000, 100)

print(x_data_train_seq[0])
# 기존 218개의 요소를 가지는 list를 100개의 요소를 가지는 ndarray로 변환
# default 설정은 뒷쪽 부분을 남기고 앞쪽 부분을 잘라낸다.

# [  2  33   6  22  12 215  28  77  52   5  14 407  16  82   2   8   4 107
#  117   2  15 256   4   2   7   2   5   2  36  71  43   2 476  26 400 317
#   46   7   4   2   2  13 104  88   4 381  15 297  98  32   2  56  26 141
#    6 194   2  18   4 226  22  21 134 476  26 480   5 144  30   2  18  51
#   36  28 224  92  25 104   4 226  65  16  38   2  88  12  16 283   5  16
#    2 113 103  32  15  16   2  19 178  32]

~~~

`maxlen` 속성에 원하는 길이를 지정하면 이보다 긴 경우는 잘라내고 짧은 경우는
0으로 `padding`합니다. x_data_train은 python list로 구성된 1차원 ndarray였는데
`x_data_train_seq`는 (25000, 100)의 shape을 가지는 2차원 ndarray가 되었습니다.
<br><br>

그림으로 표현하면 다음과 같겠죠. 그림에서 샘플의 개수가 20000개 이지만 우리 예에서
샘플의 개수는 25000개 입니다.

{% include image.html
file='deep-learning-imdb-review-sample-padding.png'
%}
<br>

위에서 `pad_sequences`를 사용할 때 token의 길이가 100보다 큰 경우 잘라내고
100보다 작은 경우 0으로 padding한다고 했는데 만약 잘라낸다면 앞에서 잘라낼까요 뒤에서
잘라낼까요? 확인해보면 알겠지만

> 샘플의 앞 부분이 잘립니다. 또한 0으로 padding시 앞부분에 0이 채워지게 됩니다.

<br>

이렇게 하는 이유는 일반적으로 text는 뒤쪽에 더 중요한 데이터가 있다고 기대하기 때문입니다.
우리의 영화 Review 샘플도 끝에 결정적인 소감을 말할 가능성이 있다는 것이죠. 물론 특정 속성
(`truncating` 혹은 `padding`)의 값을 `pre`에서 `post`로 바꾸면 앞이 아닌 뒤쪽의 값을
자르거나 0을 뒤쪽에 채울 수도 있습니다.
<br><br>

이제 데이터가 준비되었으니 RNN Model을 구현해 보도록 하겠습니다.

~~~python

from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import SimpleRNN, Dense

model = Sequential()
model.add(SimpleRNN(units=8,
                    activation='tanh',
                    input_shape=(100,500)))
model.add(Dense(units=1,
                activation='sigmoid'))
                
~~~

위의 코드를 실행했을 때 다음과 같은 오류가 발생할 여지가 있습니다.

> NotImplementedError: Cannot convert a symbolic Tensor (2nd_target:0) to a numpy array

<br>

해당오류는 우리가 사용하는 numpy 버전이 1.20 버전이상일 때 발생합니다. 단순한 해결방법은
numpy버전을 1.19 버전으로 다운그레이드 해주면 됩니다.

> `conda install numpy=1.19`

~~~python

import numpy as np
print(np.__version__)

# 1.19.2

~~~

정상적으로 수행됬으면 이제 코드를 이해해보도록 하죠.
<br><br>

`SimpleRNN` layer를 추가할 때 `input_shape` 매개변수의 값으로 `(100,500)`을
사용했습니다. 첫번재 차원인 100의 의미는 우리의 샘플의 길이(time step)가 100이기 때문입니다.
그러면 뒤에 500은 어떤 의미일까요?
<br><br>

위에서 만든 `x_data_train_seq` 중 하나의 샘플값을 출력해보면 다음과 같습니다.

~~~python

from tensorflow.keras.preprocessing.sequence import pad_sequences

x_data_train_seq = pad_sequences(x_data_train, maxlen=100)

print(type(x_data_train_seq), x_data_train_seq.shape) 
# <class 'numpy.ndarray'> (25000, 100)

print(x_data_train_seq[0])

# [  2  33   6  22  12 215  28  77  52   5  14 407  16  82   2   8   4 107
#  117   2  15 256   4   2   7   2   5   2  36  71  43   2 476  26 400 317
#   46   7   4   2   2  13 104  88   4 381  15 297  98  32   2  56  26 141
#    6 194   2  18   4 226  22  21 134 476  26 480   5 144  30   2  18  51
#   36  28 224  92  25 104   4 226  65  16  38   2  88  12  16 283   5  16
#    2 113 103  32  15  16   2  19 178  32]

~~~

`token`을 정수로 변환한 이 값을 그대로 신경망에 주입하면 큰 정수가 큰 활성화 출력을 만들기
때문에 적절치 않습니다. 또한 이 값들은 그 크기에 어떤 의미가 없습니다. 즉 20번 token을
10번 token보다 중요하게 여겨야 할 이유가 없다는 말이죠. 따라서 이 값을 신경망에 입력하기
위해서는 다른 방법이 필요합니다.
<br><br>

정수값에 있는 크기속성을 없애고 각 정수를 고유하게 표현하는 방법은 무엇일까요? 우리가 이전에
배웠던 `one-hot encoding`을 이용하면 됩니다.
<br><br>

그렇다면 각각의 정수를 `one-hot encoding`으로 표현하려면 전체 배열의 길이는 얼마가 되어야
할까요? 당연히 우리의 전체 단어개수인 `500`개 입니다. (우리는 단어의 수를 500개만 사용한다고
데이터를 로딩할 때 명시했었습니다.)
<br><br>

이제 이 `500`이라는 숫자의 의미를 이해하시겠죠?
<br><br>

그럼 결국 model에 주입되는 우리의 데이터는 `one-hot encoding`으로 표현되는 데이터여야 합니다.
당연한 얘기지만 keras는 이런 `one-hot`처리를 위한 함수를 제공해 줍니다. 아래의 코드를 보시죠.

~~~python

from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical

x_data_train_seq = pad_sequences(x_data_train, maxlen=100)

print(type(x_data_train_seq), x_data_train_seq.shape) 
# <class 'numpy.ndarray'> (25000, 100)

print(x_data_train_seq[0])

# [  2  33   6  22  12 215  28  77  52   5  14 407  16  82   2   8   4 107
#  117   2  15 256   4   2   7   2   5   2  36  71  43   2 476  26 400 317
#   46   7   4   2   2  13 104  88   4 381  15 297  98  32   2  56  26 141
#    6 194   2  18   4 226  22  21 134 476  26 480   5 144  30   2  18  51
#   36  28 224  92  25 104   4 226  65  16  38   2  88  12  16 283   5  16
#    2 113 103  32  15  16   2  19 178  32]

x_data_train_onehot = to_categorical(x_data_train_seq)

print(x_data_train_onehot.shape)
# (25000, 100, 500)

~~~

정수하나마다 500개의 요소를 가진 배열로 변경되었기 때문에 모델에 주입되는 데이터의
최종 shape은 `(25000, 100, 500)` 형태라고 볼 수 있습니다.
<br><br>

자 그럼 지금까지 내용을 정리해서 전체 코드를 한번 작성하고 신경망을 훈련시켜보도록
하겠습니다.


~~~python

import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from tensorflow.keras.datasets import imdb
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import SimpleRNN, Dense
from tensorflow.keras.optimizers import RMSprop
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping

# Data Loading
(x_data_train, t_data_train), (x_data_test, t_data_test) = \
imdb.load_data(num_words=500)

print(x_data_train.shape, t_data_train.shape)   # (25000,) (25000,)
print(x_data_test.shape, t_data_test.shape)   # (25000,) (25000,)

# train data, validation data 분리
x_data_train, x_data_valid, t_data_train, t_data_valid = \
train_test_split(x_data_train,
                 t_data_train,
                 test_size=0.2,
                 random_state=42,
                 stratify=t_data_train)

# pad_sequences를 이용하여 review의 단어길이(token)을 100으로 설정
x_data_train_seq = pad_sequences(x_data_train, maxlen=100)
x_data_valid_seq = pad_sequences(x_data_valid, maxlen=100)

print(x_data_train_seq.shape, x_data_valid_seq.shape) 
# (20000, 100) (5000, 100)

# x_data의 형태를 one-hot encoding으로 변경
x_data_train_onehot = to_categorical(x_data_train_seq)
x_data_valid_onehot = to_categorical(x_data_valid_seq)

print(x_data_train_onehot.shape, x_data_valid_onehot.shape)
# (20000, 100, 500) (5000, 100, 500)

# Model 

model = Sequential()

model.add(SimpleRNN(units=8,
                    activation='tanh',
                    input_shape=(100,500)))

model.add(Dense(units=1,
                activation='sigmoid'))

model.compile(optimizer=RMSprop(learning_rate=1e-4),
              loss='binary_crossentropy',
              metrics=['accuracy'])

checkpoint_cb = ModelCheckpoint(filepath='./best-simplernn-model.h5',
                                monitor='val_loss',
                                save_best_only=True,
                                mode='auto')

early_stopping_cb = EarlyStopping(monitor="val_loss",
                                  patience=3,
                                  verbose=1,
                                  restore_best_weights=True)

history = model.fit(x_data_train_onehot,
                    t_data_train,
                    epochs=100,
                    batch_size=64,
                    validation_data=(x_data_valid_onehot, t_data_valid),
                    callbacks=[checkpoint_cb, early_stopping_cb])

# Epoch 1/100
# 313/313 [==============================] - 6s 18ms/step - loss: 0.6820 - accuracy: 0.5623 - val_loss: 0.6599 - val_accuracy: 0.6142
# Epoch 2/100
# 313/313 [==============================] - 5s 17ms/step - loss: 0.6437 - accuracy: 0.6388 - val_loss: 0.6287 - val_accuracy: 0.6634
# Epoch 3/100
# 313/313 [==============================] - 5s 17ms/step - loss: 0.6169 - accuracy: 0.6786 - val_loss: 0.6068 - val_accuracy: 0.6970
# ...                
# Epoch 29/100
# 313/313 [==============================] - 5s 17ms/step - loss: 0.4211 - accuracy: 0.8181 - val_loss: 0.4611 - val_accuracy: 0.7878
# Epoch 30/100
# 310/313 [============================>.] - ETA: 0s - loss: 0.4195 - accuracy: 0.8179Restoring model weights from the end of the best epoch.
# 313/313 [==============================] - 5s 17ms/step - loss: 0.4194 - accuracy: 0.8179 - val_loss: 0.4615 - val_accuracy: 0.7840
# Epoch 00030: early stopping                

~~~

정확도는 약 80% 정도 나오네요. `history`객체를 이용해서 loss와 accuracy에 대한 그래프를
그려보도록 하겠습니다.

~~~python

figure = plt.figure()

ax1 = figure.add_subplot(1,2,1)
ax2 = figure.add_subplot(1,2,2)

ax1.plot(history.history['loss'], 'o', color='r', label='training loss')
ax1.plot(history.history['val_loss'], color='b', label='validation loss')
ax1.set_title('Training and Validation Loss')
ax1.legend()

ax2.plot(history.history['accuracy'], 'o', color='r', label='training accuracy')
ax2.plot(history.history['val_accuracy'], color='b', label='validation accuracy')
ax2.set_title('Training and Validation Accuracy')
ax2.legend()

plt.tight_layout()
plt.show()

~~~

{% include image.html
file='deep-learning-imdb-rnn-loss-accuracy-graph.png'
%}
<br>

이 구현에서 한가지 생각해봐야 할 점이 있습니다. 위의 코드를 잘 살펴보면 학습을 하기 위해
우리는 `one-hot encoding`을 사용했습니다. 당연히 데이터의 크기가 커지게 됩니다.
`one-hot encoding`은 우리에게 상당히 편리함을 안겨주지만 그 반대급부로 데이터량이 엄청나게
증가하게 됩니다. 우리는 `token` 1개를 500개의 차원으로 늘렸기 때문에 실제 데이터량도 500배가
증가한 것이라고 볼 수 있는 것이죠. 이 문제를 어떻게 해결해야 할까요?

---

## Word Embedding

`RNN`에서 text를 처리할 때 즐겨 사용하는 방법이 바로 `Word Embedding`(단어 임베딩)입니다.
<br><br>

`one-hot encoding`은 표현하고자 하는 단어의 index만 1로 표현하고 나머지 index는
모두 0으로 표현하는 vector 표현방법입니다. 이렇게 vector나 matrix 대부분의 값이 0으로
표현되는 방법을 우리는 `sparse representation`(희소 표현)이라고 합니다.
즉, `one-hot vector`는 `sparse vector`(희소 벡터)입니다.
<br><br>

위에서 언급했지만 `sparse vector`의 문제점은 단어의 개수가 늘어나면 vector의 차원이 계속
커져야 한다는 점입니다. 만약 `corpus`(말뭉치)에 단어가 10,000개 이면 vector의 차원은 10,000
이어야 하기 때문입니다. 이러한 vector 표현은 당연히 공간의 낭비를 가져오게 되겠죠.
<br><br>

이런 `sparse representation`에 반대되는 표현이 `dense representation`(밀집 표현)입니다.
밀집 표현은 vector의 차원을 단어 집합의 크기로 산정하지 않습니다. 사용자가 설정한 값으로
모든 vector의 차원을 맞춰 사용합니다. 그리고 결과로 나온 vector는 0과 1만으로 이루어진
vector가 아닌 실수값을 가지게 됩니다. 이 경우 vector의 차원이 조밀해졌다고 해서 `dense vector`라는
표현을 사용합니다.
<br><br>

단어를 `dense vector`형태로 표현하는 방법을 `word embedding`이라고 합니다. 이 워드 임베딩
방법론은 `LSA`, `Word2Vec`, `FastText`, `Glove`등이 있습니다. 나중에 우리가 구현에서 사용할
keras의 `Embedding`은 이런 방법론을 사용하지는 않지만 단어를 랜덤한 값을 가지는 `dense vector`로
변환한 후 neural network의 가중치를 학습하는 방식으로 이 단어 vector를 학습하는 방법을 사용합니다.
<br><br>

결론적으로 `Word Embedding`은 각 단어를 고정된 크기의 실수 vector로 바꾸는 작업을 의미합니다.
<br><br>

아래의 그림처럼 말이죠.

{% include image.html
file='deep-learning-imdb-rnn-word-embedding-cat.png'
%}
<br>

이런 `Word Embedding`은 `one-hot encoding`보다 훨씬 의미있는 값으로 채워져 있기 때문에
자연어 처리에서 더 좋은 성능을 내는 경우가 많습니다. `keras`에서는 `Embedding`이라는 이름의
layer로 임베딩 기능을 제공합니다. 이 layer는 입력으로 정수를 받는다는 점도 같이 기억해야 합니다.
모델을 훈련시킬 때 입력데이터를 기존과 다르게 주어야 한다는 의미죠.
<br><br>

간단하게 코드로 살펴보죠

~~~python

from tensorflow.keras.layers import Embedding

model = Sequential()

model.add(Embedding(input_dim=500, 
                    output_dim=16,
                    input_length=100))

model.add(SimpleRNN(units=8,
                    activation='tanh'))

model.add(Dense(units=1,
                activation='sigmoid'))

~~~

`Embedding` layer의 첫번째 파라미터인 `input_dim`(500)은 어휘 사전의 크기입니다. 우리는
IMDB Review data set에서 500개의 단어만을 사용하기로 했던거 기억하실 겁니다. 두번째 파라미터인
`output_dim`(16)은 `embedding vector`의 크기입니다. 당연히 one-hot 보다는 훨씬 작습니다.
세번째 파라미터인 `input_length`(100)은 입력 sequence의 길이입니다. 우리의 time step은 100인걸
떠올리시면 됩니다.
<br><br>

자 그럼 모델만 변경한 후 다시 학습을 진행해 보도록 하겠습니다.

~~~python

import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from tensorflow.keras.datasets import imdb
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import SimpleRNN, Dense, Embedding
from tensorflow.keras.optimizers import RMSprop
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping

# Data Loading
(x_data_train, t_data_train), (x_data_test, t_data_test) = \
imdb.load_data(num_words=500)

print(x_data_train.shape, t_data_train.shape)   # (25000,) (25000,)
print(x_data_test.shape, t_data_test.shape)   # (25000,) (25000,)

# train data, validation data 분리
x_data_train, x_data_valid, t_data_train, t_data_valid = \
train_test_split(x_data_train,
                 t_data_train,
                 test_size=0.2,
                 random_state=42,
                 stratify=t_data_train)

# pad_sequences를 이용하여 review의 단어길이(token)을 100으로 설정
x_data_train_seq = pad_sequences(x_data_train, maxlen=100)
x_data_valid_seq = pad_sequences(x_data_valid, maxlen=100)

print(x_data_train_seq.shape, x_data_valid_seq.shape) 
# (20000, 100) (5000, 100)

# x_data의 형태를 one-hot encoding으로 변경
# x_data_train_onehot = to_categorical(x_data_train_seq)
# x_data_valid_onehot = to_categorical(x_data_valid_seq)

# print(x_data_train_onehot.shape, x_data_valid_onehot.shape)
# (20000, 100, 500) (5000, 100, 500)

# Model 

model = Sequential()

model.add(Embedding(input_dim=500, 
                    output_dim=16,
                    input_length=100))

model.add(SimpleRNN(units=8,
                    activation='tanh'))

model.add(Dense(units=1,
                activation='sigmoid'))

model.compile(optimizer=RMSprop(learning_rate=1e-4),
              loss='binary_crossentropy',
              metrics=['accuracy'])

checkpoint_cb = ModelCheckpoint(filepath='./best-simplernn-model.h5',
                                monitor='val_loss',
                                save_best_only=True,
                                mode='auto')

early_stopping_cb = EarlyStopping(monitor="val_loss",
                                  patience=3,
                                  verbose=1,
                                  restore_best_weights=True)

history = model.fit(x_data_train_seq,
                    t_data_train,
                    epochs=100,
                    batch_size=64,
                    validation_data=(x_data_valid_seq, t_data_valid),
                    callbacks=[checkpoint_cb, early_stopping_cb])

# Epoch 31/100
# 313/313 [==============================] - 4s 12ms/step - loss: 0.3943 - accuracy: 0.8313 - val_loss: 0.4700 - val_accuracy: 0.7804
# Epoch 32/100
# 313/313 [==============================] - 4s 12ms/step - loss: 0.3916 - accuracy: 0.8327 - val_loss: 0.4814 - val_accuracy: 0.7784
# Epoch 33/100
# 311/313 [============================>.] - ETA: 0s - loss: 0.3896 - accuracy: 0.8348Restoring model weights from the end of the best epoch.
# 313/313 [==============================] - 4s 11ms/step - loss: 0.3894 - accuracy: 0.8349 - val_loss: 0.4850 - val_accuracy: 0.7748
# Epoch 00033: early stopping     

~~~

모델의 성능은 비슷하지만 사용하는 메모리 양이나 속도면에서는 차이가 많이 있다는 것을 알 수 있습니다.


End.

{% include links.html %}
