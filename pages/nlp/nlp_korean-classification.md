---
title: Natural Language Processing 한국어 감성분류
sidebar: nlp_sidebar
summary: "Naver Review 한국어 감성분류 구현입니다."
permalink: nlp_korean-classification.html
folder: nlp
---

## 알아두어야 할 추가적인 개념들

이전장에서 RNN Text 감성분류의 대표적 예제인 IMDB Review 분류에 대해서 SimpleRNN을 이용해
구현해 봤습니다. 학습과 정확도 측정까지는 구현이 되었지만 특정 문장을 이용한 예측은 수행할 수
없었습니다. 특정 Review Text로 예측을 해 보려면 해당 Text를 Embedding 시켜야 하는데 이 방법이
없었죠.
<br><br>

이번에는 내용을 조금 확장해서 한국어 Review에 대한 감성분류를 구현해 보겠습니다.
필요한 개념들을 먼저 살펴보도록 하죠.

### LSTM

순환신경망 모델을 처음 배울때는 `RNN`구조부터 배우지만 실무적으로는 `SimpleRNN`보다 성능이
뛰어난 `LSTM`이나 `GRU`를 사용합니다.
<br><br>

`LSTM`의 자세한 구조는 차후에 다시 설명하기로 하고 여기서는 간단하게 설명하고 넘어가도록 하겠습니다.
<br><br>

Text 처리 시 입력 문장이 길이가 길어질수록 `SimpleRNN`은 멀리 떨어진 단어와의 관련성을 파악하기
어려워 성능이 떨어지는 단점이 있습니다. 즉, 과거 정보가 마지막까지 전달되지 못하는
`long-term dependency problem`(장기의존성 문제)이 발생하게 됩니다.
<br><br>

이 문제를 해결하고자 나온 방법이 바로 `LSTM`(Long Short-Term Memory)입니다. 아주 쉽게 말하자면
장단기 메모리를 활용해 잊어야 할 것과 기억해야 할 것을 조절하는 방식으로 이 문제를 해결합니다.
실제 `LSTM`의 내용은 상당히 복잡하지만 `keras`를 이용한 구현 자체는 상당히 간단합니다.
`SimpleRNN`을 `LSTM`으로 대체만 해 주면 되니까요.

### GRU

`LSTM`은 cell 구조가 복잡하고 속도가 느리다는 단점이 있습니다. 이를 보안하기 위해 `LSTM`의
구조를 단순화한 모델인 `GRU`가 제안되었습니다. 아주 쉽게 생각하면 `LSTM`의 단순화 버전이라고
생각해도 무방합니다.
<br><br>

일반적으로 데이터 양이 조금 더 적으면 `GRU`가 데이터 양이 많으면 `LSTM`이 더 성능이 좋다고
알려져 있지만 데이터의 상태와 환경에 따라 달라질 수 있습니다.

### Bidirectional RNN

소위 양방향 RNN이라고 불리는 `Bidirectional RNN`은 자연어 처리 시 순서대로 데이터를 처리하고
역순으로 처리를 추가할 경우 더 좋은 성능을 발휘할 수 있을 때 사용됩니다.
<br><br>

시점 t에서의 출력값을 예측할 때 이전 시점의 데이터뿐만아니라 이후 데이터로도
예측할 수 있다는 아이디어에 기반합니다.
<br><br>

이해하기 쉽도록 다음의 문제를 문제를 보도록 하죠.

~~~text

Exercise is very effective at [          ] belly fat.

1) reducing
2) increasing
3) multiplying

~~~

`운동은 복부 지방을 [] 하기에 효과적이다 `라는 영어 문장이고 정답은 당연히 reduction입니다. 하지만
정답을 맞추려면 이전 단어들만으로는 부족하고 다음 단어를 알아야 정답을 결정할 수 있습니다. 이런 경우
양방향 RNN이 유효합니다.
<br><br>

양방향 RNN은 하나의 출력값을 예측하기 위해 기본적으로 두 개의 메모리 cell을 사용합니다. 하나는 앞 시점의
은닉상태를 전달받는 용도로 다른 하나는 뒤 시점의 은닉 상태를 전달받아 현재의 은닉 상태를 계산하게 됩니다.
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='deep-learning-bidirectional-rnn-concept.png'
%}
<br>

그림에서 알 수 있듯이 만약 `LSTM`을 `Bidirectional RNN`로 구현하게 되면 units의 수는
2배가 되게 됩니다. 순방향과 역방향으로 각각 `LSTM`이 만들어지기 때문입니다.
<br><br>

이 `Bidirectional RNN`을 tensorflow keras로 표현하면 다음과 같습니다.

~~~python

from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import SimpleRNN, Bidirectional

model = Sequential()
model.add(Bidirectional(SimpleRNN(hidden_size, 
                                  return_sequences=True), 
                                  input_shape=(timesteps, input_dim)))

~~~

### Stacked RNN

`Dense` layer처럼 `RNN` layer 또는 `Bidirectional RNN`을 여러 층으로 쌓아서
모델을 만들 수 있습니다. 이때 중요한 점이 있는데 다른 layer처럼 쌓기만 해서는 안되는다는
점입니다.
<br><br>

기본적으로 RNN은 마지막 `hidden state`만 출력하기 때문이죠. 다음 그림과 같습니다.
(이전에도 한번 설명했었습니다.)

{% include image.html
file='deep-learning-rnn-output-figure.png'
%}
<br>

layer를 쌓기 위해서는 위의 그림에서 1~3 출력도 허용해야 합니다. 이때 사용하는
파라미터가 바로 `return_sequences`이고 이 값을 `True`로 설정해야 합니다.
<br><br>

당연히 마지막 최상단의 RNN layer는 더 이상 모든 `hidden state`를 전달할 필요가 없기 때문에
`return_sequences`를 `False`로 설정해야 합니다. (`False`가 기본값입니다.)
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='deep-learning-stacked-rnn-output-figure.png'
%}

### Recurrent Dropout

Tensorflow에서 기본적으로 제공하는 `dropout`외에 RNN은 `recurrent_dropout`을
제공하고 있습니다. `LSTM`의 파라미터 중 `dropout`은 입력에 대한 dropout 비율을 의미하고
`recurrent dropout`은 순환 상태의 dropout 비율을 의미합니다. 둘 다 0과 1사이의 값이고
당연히 과대적합을 방지하기 위해 내장된 기능입니다.
<br><br>

코드로 표현하면 다음과 같습니다.

~~~python

from tensorflow.keras.layers import LSTM

model = Sequential()

model.add(Embedding(input_dim=500, 
                    output_dim=16,
                    input_length=100))

model.add(LSTM(units=8,
               activation='tanh',
               dropout=0.2,
               recurrent_dropout=0.2))

model.add(Dense(units=1,
                activation='sigmoid'))

~~~

---

## 한국어 감성분류 구현

`NLP`(자연어 처리, Natural Language Processing)는 스팸, 뉴스 분류, 텍스트 요약,
문장 생성, 기계 번역, 챗봇, 감성 분류등 다양한 분야에 활용되고 있습니다. 일단 먼저
자연어를 처리하는 방법을 살펴본 후 `RNN`을 이용하여 감성 분류 모델을 직접 구현해 보도록 하죠.

### 자연어 처리 방법

텍스트 데이터는 구조화되어 있지도 않고 데이터의 길이가 일정하지 않습니다. 따라서 문장의
길이가 다를 경우 deep learning 모델에 입력으로 넣기 위해서는 길이를 동일하게 맞추는 작업이
선행되어야 합니다.
<br><br>

또한 한글을 입력 데이터로 활용하기 위해서는 한글을 숫자로 변환해야 하고, 한글과 숫자를 1:1 매칭하는
방식으로 `Vocabulary`(단어사전, 어휘사전)를 만들어야 합니다. 이 단어사전은 모든 단어(token)를
숫자로 매핑한 사전을 의미합니다.
<br><br>

이렇게 단어(token)를 숫자로 변경하기 위해서는 문장을 특정한 기준으로 잘라내야 합니다. 이렇게 잘라낸
조각을 우리는 `token`이라고 부르죠. 영어는 이 token을 만드는 과정이 비교적 간단합니다. 띄어쓰기를
기준으로 잘라내면 되기 때문이죠. 하지만 한글은 조사(~가, ~을, ~처럼 같은)가 붙어 있어서 잘라내는
작업에 어려움이 있습니다. `token`을 잘라내는 역할을 하는 것을 `tokenizer`라고 하는데 이
`tokenizer`가 불용어(`stopword`) 처리를 통해 조사와 같은 불필요한 단어를 제거하는 기능을 수행합니다.
<br><br>

간단하게 정리하면 문장 텍스트 데이터를 deep learning 모델에 입력으로 넣기 위해서 다음과 같은
작업이 필요합니다.
<br><br>

1. 토큰화(tokenizer를 이용하여 문장을 띄어쓰기로 구분하고 불용어 처리)
2. 단어 사전 작성(단어와 숫자를 매핑)
3. 문자 인코딩(단어 사전을 바탕으로 문장들을 숫자로 변경)
4. `padding`(인코딩된 문장 길이를 동일하게 변경)

<br>

아래의 그림은 두 개의 문장을 인코딩과 패딩을 통해 동일한 길이의 데이터로 만드는 것에 대한 예시입니다.

{% include image.html
file='deep-learning-rnn-statement-encoding-process.png'
%}
<br>

단어사전을 만들 때 단어의 순서가 아니라 일반적으로 단어의 빈도를 이용해 높은 빈도를 가지는 단어가
앞 번호를 가지도록 만듭니다.
<br><br>

tensorflow keras에서 제공하는 `Tokenizer`는 띄어쓰기를 기준으로 단어 사전을 생성하고 단어를
쉽게 encoding할 수 있도록 도와줍니다. 위의 그림에 있는 문장을 이용하여 코드를 작성하고 수행시켜
보도록 하겠습니다.

~~~python

from tensorflow.keras.preprocessing.text import Tokenizer

sentences = [
    '영실이는 나를 정말 정말 좋아해',
    '영실이는 영화를 좋아해'
]

tokenizer = Tokenizer()
tokenizer.fit_on_texts(sentences)

print('단어 인덱스 : ', tokenizer.word_index)
# 단어 인덱스 :  {'영실이는': 1, '정말': 2, '좋아해': 3, '나를': 4, '영화를': 5}

# texts_to_sequences() 함수는 입력된 문장을 단어 index를 사용해
# 숫자 vector로 변환한다.

word_encoding = tokenizer.texts_to_sequences(sentences)
print(word_encoding)
# [[1, 4, 2, 2, 3], [1, 5, 3]]

~~~

만약 단어 사전에 없는 새로운 단어가 등장하면 새로운 단어는 `encoding`할 때 무시됩니다.
다음 코드를 보시죠.

~~~python

new_sentences = ['영실이는 경록이와 나를 좋아해']
new_word_encoding = tokenizer.texts_to_sequences(new_sentences)
print(new_word_encoding)
# [[1, 4, 3]]   4개의 token이 도출되지만 encoding된 결과는 3개이다.

~~~

사전에 없는 단어가 등장하면 어떻게 할까? 위의 예처럼 그냥 없애버리면 되나?
사전에 존재하지 않는 단어를 OOV(out of vocabulary) token이라고 합니다.
keras Tokenizer는 이 `OOV token`을 처리하기 위해 파라미터를 설정할 수 있습니다.

~~~python

tokenizer = Tokenizer(oov_token='<OOV>')
tokenizer.fit_on_texts(sentences)

print('단어 인덱스 : ', tokenizer.word_index)
# 단어 인덱스 :  {'<OOV>': 1, '영실이는': 2, '정말': 3, '좋아해': 4, '나를': 5, '영화를': 6}

new_sentences = ['영실이는 경록이와 나를 좋아해']
new_word_encoding = tokenizer.texts_to_sequences(new_sentences)
print(new_word_encoding)
# [[2, 1, 5, 4]]

~~~

텍스트 데이터셋어 빈도수가 작은 단어가 많이 존재하는 경우에는 이들 단어를 제외하는게 일반적입니다.
즉, 문장을 token으로 인코딩할 때 빈도수가 많은 순서대로 최대 사전 개수를 정하고 빈도수가 적은
단어를 제외시킵니다. 최대 사전 개수는 `num_words` 파라미터로 설정할 수 있습니다. 아래의 코드를
보시죠

~~~python

tokenizer = Tokenizer(num_words=3, 
                      oov_token='<OOV>')
tokenizer.fit_on_texts(sentences)

print('단어 인덱스 : ', tokenizer.word_index)
# 단어 인덱스 :  {'<OOV>': 1, '영실이는': 2, '정말': 3, '좋아해': 4, '나를': 5, '영화를': 6}

new_sentences = ['영실이는 경록이와 나를 좋아해']
new_word_encoding = tokenizer.texts_to_sequences(new_sentences)
print(new_word_encoding)
# [[2, 1, 1, 1]]

~~~

순환신경망에 데이터를 입력으로 넣기 위해서는 문장의 길이를 동일하게 맞춰야 합니다. 이를
`padding`이라고 부르는데 아래 그림과 같습니다.

{% include image.html
file='deep-learning-rnn-pre-post_padding.png'
%}
<br>

이전에도 한번 사용해봤던 keras의 `pad_sequences` 함수를 이용하면 이 작업을 쉽게 할 수 있습니다.

~~~python

from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

sentences = [
    '영실이는 나를 정말 정말 좋아해',
    '영실이는 영화를 좋아해'
]

tokenizer = Tokenizer(oov_token='<OOV>')
tokenizer.fit_on_texts(sentences)

print('단어 인덱스 : ', tokenizer.word_index)
# 단어 인덱스 :  {'<OOV>': 1, '영실이는': 2, '정말': 3, '좋아해': 4, '나를': 5, '영화를': 6}

word_encoding = tokenizer.texts_to_sequences(sentences)
print(word_encoding)
# [[2, 5, 3, 3, 4], [2, 6, 4]]

padded = pad_sequences(word_encoding, 
                       maxlen=4)
print(padded)
# [[5 3 3 4]
#  [0 2 6 4]]


~~~

자연어 처리를 위해서는 `Embedding`과정이 반드시 필요합니다. `Word2Vec`과 같은 기법을 이용할
수 있지만 우리는 tensorflow keras를 이용하니 keras에서 제공하는 `Embedding layer`를 사용하도록
하겠습니다.

### 데이터 로딩

기본적인 내용은 배웠으니 이제 실제로 구현을 해 보죠.
<br><br>

먼저 필요한 module을 불러옵니다.

~~~python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import tensorflow as tf
import warnings
warnings.filterwarnings(action='ignore')

~~~

사용할 데이터 셋은 네이버 영화 리뷰 데이터를
이용하도록 하겠습니다. 다음의 코드로 파일을 받을 수 있습니다.

~~~python

# Naver sentiment movie corpus V1.0 

# 파일의 저장 경로는 ~/.keras/datasets/ 입니다.
# ratings_train.txt는 train 파일이고
# ratings_test.txt는 test 파일입니다. 

train_file = tf.keras.utils.get_file(
    './ratings_train.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_train.txt',
    extract=True
)

train_df = pd.read_csv(train_file, sep='\t')
display(train_df.head(), train_df.shape)  # (150000, 3)

test_file = tf.keras.utils.get_file(
    './ratings_test.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_test.txt',
    extract=True
)

test_df = pd.read_csv(test_file, sep='\t')
display(test_df.head(), test_df.shape)  # (50000, 3)

~~~

아래의 링크를 이용하면 zip 파일로 되어 있는 text파일을 다운받으실 수 있습니다.
<br><br>

[Naver sentiment movie corpus V1.0(train)](https://drive.google.com/file/d/1pYz5T52I73HLhoJaI86c1kBUEO1gWPa5/view?usp=sharing){: target="_blank" }
<br><br>

[Naver sentiment movie corpus V1.0(test)](https://drive.google.com/file/d/1BVFVtZQwtzFgSxf45q5KLOcOVCkUgUeV/view?usp=sharing){: target="_blank" }
<br><br>

DataFrame의 내용을 간단히 살펴보면 다음과 같습니다.

{% include image.html
file='deep-learning-rnn-naver-sentiment-analysis-corpus.png'
%}
<br>

총 200,000개의 데이터가 있으며 train 데이터가 150,000개 test 데이터가 50,000개로 구성되어
있습니다. 모든 Review의 길이는 140자 이내이며, 실제 영화 리뷰 점수는 1점에서 10점으로 이루어져
있습니다. 이 중 9점~10점을 긍정적인 Review, 1점~4점을 부정적 Review로 분류한 데이터 입니다.
(참고로 5점~8점의 데이터는 포함하지 않았습니다.)

### EDA(탐색적 데이터분석)

간단한 `EDA`(탐색적 데이터분석)를 해 보죠. 전체 데이터를 파악하고 전처리가 필요한 부분을 찾아봐야
합니다. 여기서는 데이터 크기 및 샘플 확인, 레이블 비율, 결측치등을 확인해 보겠습니다.

~~~python

train_df = pd.read_csv(train_file, sep='\t')
display(train_df.head(), train_df.shape)  # (150000, 3)

# 감성분석을 위한 텍스트분류 모델에서 필요한 값은 document와 label입니다. 
# 총 150,000개의 데이터가 존재합니다. 

# label에 들어 있는 긍정과 부정의 비율을 확인해야 합니다. 참고로 데이터의 불균형이
# 발생하면 훈련이 정상적으로 이루어지기 어렵습니다. 

cnt = train_df['label'].value_counts()
print(cnt)

# 0    75173
# 1    74827
# Name: label, dtype: int64

# 0(부정)의 개수가 75,173개 1(긍정)의 개수가 74,82개로 비슷한 비율로
# 데이터가 구성되어 있음을 확인할 수 있습니다. 

# 결측치 확인

print(train_df.isnull().sum())

# id          0
# document    5
# label       0
# dtype: int64

# Review글이 없는 데이터가 5개가 존재합니다. 의미가 없는 데이터이기 때문에
# 결측치가 있는 데이터는 추후에 삭제해야 합니다. 

~~~

### 형태소 분석

한국어 형태소 분석기는 여러가지가 개발되어 있습니다. 대표적으로 `Kham`, `Komoran`,
`Okt`, `Mecab` 등이 있습니다. 영어는 띄어쓰기가 잘 되어 있는 편이지만 한글은 띄어쓰기,
맞춤법이 잘못됬을 경우 분리에 어려움이 많습니다. 또한 정확한 형태소 분석을 위해서는 전처리
과정이 필요할 수 있습니다.
<br><br>

자 그럼 `Windows 10` 환경에서 `KoNLPy`를 설치해 보도록 하죠.
<br><br>

1. 제일 먼저 `Java`를 설치해야 합니다. 아래의 링크를 이용해 들어가서 설치파일을 다운로드 받습니다.<br>
   [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/){: target="_blank" }<br>
   아래의 링크를 이용해 설치파일을 다운로드 해도 됩니다.<br>
   [JDK 18 x64 Installer](https://drive.google.com/file/d/12qqEaR6q0itSUWAPaHowJLW-4tFyQfbT/view?usp=sharing){: target="_blank" }

2. 환경변수 설정을 해야 합니다. `JAVA_HOME` 환경변수를 설정합니다.<br>
   `JAVA_HOME=C:\Program Files\Java\jdk-18.0.1.1`

3. Java와 Python을 연결하기 위해 `jpype`을 설치해야 합니다. Python 버전에 맞게 다운로드 후 설치합니다. <br>
   우리가 사용하는 python 버전은 3.8이기 때문에 해당 버전에 맞게 다운로드 해야 합니다. <br>
   또한 호환성문제로 `1.1.2` 버전을 설치합니다.<br>
   아래의 링크를 이용해 파일을 다운로드 합니다.<br>
   [jpype Downloads](https://www.lfd.uci.edu/~gohlke/pythonlibs/#jpype){: target="_blank" }<br>
   아래의 링크를 이용해 파일을 다운로드 해도 됩니다.<br>
   [JPype1-1.1.2-cp38-cp38-win_amd64.whl](https://drive.google.com/file/d/1CJzbX0g9-qgcKKSR8zdXCJ6t5W3zBQe6/view?usp=sharing){: target="_blank" }<br>
   `Anaconda` 가상환경을 활성화 시킨 상태에서 `pip`를 이용하여 파일이 있는 위치에서 설치합니다.<br>
   그런다음 pip를 이용해 `konlpy`를 설치합니다.

{% include image.html
file='deep-learning-rnn-jpype-install.png'
%}
<br>

설치가 잘 되었는지 다음 코드로 확인해보죠.

~~~python

from konlpy.tag import Okt, Komoran, Kkma, Mecab

kkma = Kkma()
okt = Okt()
komoran = Komoran()

# 주의!!!! 
# KoNLPy’s Mecab() class is not supported on Windows machines.
# mecab = Mecab()

text = '이것은 소리없는 아우성. 저 푸른 해원을 향하여 흔드는 영원한 노스탤지어의 손수건'

# morphs() : 텍스트에서 형태소를 반환합니다. 
# pos() : 텍스트에서 품사 정보를 부착하여 반환합니다.

print(kkma.morphs(text))
# ['이것', '은', '소리', '없', '는', '아우성', '.', '저', '푸른', '해원', '을', '향하', '여', 
#  '흔들', '는', '영원', '하', 'ㄴ', '노스탤지어', '의', '손수건']

print(kkma.pos(text))
# [('이것', 'NP'), ('은', 'JX'), ('소리', 'NNG'), ('없', 'VA'), ('는', 'ETD'), ('아우성', 'NNG'), 
#  ('.', 'SF'), ('저', 'NP'), ('푸른', 'NNP'), ('해원', 'NNG'), ('을', 'JKO'), ('향하', 'VV'), 
#  ('여', 'ECS'), ('흔들', 'VV'), ('는', 'ETD'), ('영원', 'NNG'), ('하', 'XSV'), ('ㄴ', 'ETD'), 
#  ('노스탤지어', 'NNG'), ('의', 'JKG'), ('손수건', 'NNG')]

print(okt.morphs(text))
# ['이', '것', '은', '소리', '없는', '아우성', '.', '저', '푸른', '해원', '을', '향', '하여', 
#  '흔드는', '영원한', '노스탤지어', '의', '손수건']

print(komoran.morphs(text))
# ['이것', '은', '소리', '없', '는', '아우성', '.', '저', '푸른', '해원', '을', '향하', '아', 
#  '흔들', '는', '영원', '하', 'ㄴ', '노스탤지어', '의', '손수건']

~~~

위의 결과처럼 어떠한 형태소 분석기를 사용하느냐에 따라 결과가 달라집니다. 당연히 형태소 분리의
결과가 모델 성능에 큰 영향을 주게 됩니다. 따라서 띄어쓰기와 맞춤법에 맞는 문장으로 이루어진다면
좋은 성능을 기대할 수 있습니다. 한가지 추가적으로 용량이 큰 텍스트를 사용할 경우 성능외에 속도
역시 중요한 요소가 됩니다. `Mecab`은 windows 환경에서 지원되지 않지만 `Colab`환경에서 사용할
수 있기 때문에 `Colab`에서 `Mecab`을 사용하면 빠른 속도로 조금 더 나은 성능을 기대할 수 있습니다.
`Colab`환경에서 `Mecab` 설치에 대한 내용은 구글링을 통해서 해결하시면 됩니다.

### 데이터 전처리

데이터를 확인해 보면 알겠지만 몇가지 데이터 전처리를 진행해야 합니다.
<br><br>

1. 우리의 Review 텍스트 데이터에는 특수문자, 숫자등이 포함되어 있습니다.
   정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거해야 합니다.
2. 결측치를 제거해야 합니다. (5개)
3. `stopword`(불용어)를 제거해야 합니다.
4. 단어사전을 만들고 문자를 숫자로 변경하는 token화를 진행해야 합니다.
5. 동일한 문장 길이로 정리해야 합니다. (`padding`)

<br>

위의 내용을 코드로 표현하면 다음과 같습니다.

~~~python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import tensorflow as tf
import warnings
warnings.filterwarnings(action='ignore')

# Naver sentiment movie corpus V1.0 

train_file = tf.keras.utils.get_file(
    './ratings_train.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_train.txt',
    extract=True
)

train_df = pd.read_csv(train_file, sep='\t')
display(train_df.head(), train_df.shape)  # (150000, 3)


# 1. 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
train_df['document'] = train_df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')
display(train_df.head())  

# 2. 결측지 제거 (5개)
train_df = train_df.dropna()
print(train_df.shape) # (149995, 3) 


# 3. 불용어(stopword)제거
# 불용어는 관사, 전치사, 조사, 접속사등 의미가 없는 단어를 의미합니다. 
# 한글에는 정말 많은 불용어가 존재하는데 이 중 일부만 처리하도록 합니다. 
# 불용어를 처리하는 함수를 정의한 후 이용합니다. 
# komoran = Komoran()  komoran은 오류가 발생합니다. (Java NullPoint Exception)

from konlpy.tag import Okt, Komoran, Kkma, Mecab

okt = Okt()

def word_tokenization(text):
    stop_words = ['는', '을', '를', '이', '가', '의', '던', '고', '하', '다', 
                  '은', '에', '들', '지', '게', '도']
    return [word for word in okt.morphs(text) if word not in stop_words]

data_train = train_df['document'].apply((lambda x:word_tokenization(x)))
print(data_train)
# 0                                   [아, 더빙, 진짜, 짜증나네요, 목소리]
# 1             [흠, 포스터, 보고, 초딩, 영화, 줄, 오버, 연기, 조차, 가볍지, 않구나]
# 2                          [너, 무재, 밓었, 다그, 래서, 보는것을, 추천, 한]
# ...

# 4. 단어사전을 만들고 문자를 숫자로 변경하는 token화를 진행

from tensorflow.keras.preprocessing.text import Tokenizer

tokenizer = Tokenizer()
tokenizer.fit_on_texts(data_train)
print('총 단어 개수 : ', len(tokenizer.word_index))
# 총 단어 개수 :  102194

# print(tokenizer.word_index)
# {'영화': 1, '너무': 2, '한': 3, '정말': 4, '적': 5, '만': 6, '진짜': 7, '으로': 8, '로': 9, 
#  '점': 10, '에서': 11, '과': 12, '연기': 13, '평점': 14, '것': 15, '최고': 16, '내': 17, '그': 18, 
#  '나': 19, '잘': 20, '안': 21, '인': 22, '와': 23, '이런': 24, '스토리': 25, '생각': 26, '못': 27, 
#  '왜': 28, '드라마': 29, '이다': 30, ...

# print(tokenizer.word_counts)
# OrderedDict([('아', 4157), ('더빙', 573), ('진짜', 8338), ('짜증나네요', 23), ('목소리', 375), 
#              ('흠', 256), ('포스터', 572), ('보고', 4724), ('초딩', 424), ('영화', 50651), ('줄', 1241), 
#              ('오버', 142), ('연기', 6328), ('조차', 242), ('가볍지', 26), ('않구나', 3), ('너', 670), 
#              ('무재', 69), ('밓었', 1), ('다그', 75), ('래서', 20), ('보는것을', 10), ...

# print(tokenizer.word_counts.values())
# odict_values([4157, 573, 8338, 23, 375, 
#               256, 572, 4724, 424, 50651, 1241, 
#               142, 6328, 242, 26, 3, 670, 
#               69, 1, 75, 20, 10, ...

# 단어빈도가 5회 이상인 단어가 몇개인지 확인
def get_voca_size(threshold):
    cnt = 0
    for x in tokenizer.word_counts.values():
        if x > threshold:
            cnt = cnt + 1
    return cnt

voca_size = get_voca_size(5)
print('vocabulary size : ', voca_size)
# vocabulary size :  19338

# 단어 사전을 생성합니다.

oov_token = '<OOV>'  # 사전에 없는 단어
voca_size = 15000  # 5 이상의 빈도를 가지는 단어는 총 19,338개 이지만 15000개로 설정

tokenizer = Tokenizer(oov_token=oov_token, num_words=voca_size)
tokenizer.fit_on_texts(data_train)

# print(tokenizer.word_index)
# {'<OOV>': 1, '영화': 2, '너무': 3, '한': 4, '정말': 5, '적': 6, '만': 7, '진짜': 8, 
#  '으로': 9, '로': 10, '점': 11, '에서': 12, '과': 13, '연기': 14, '평점': 15, 
#  '것': 16, '최고': 17

print('vocabulary size : ', len(tokenizer.word_counts))
# vocabulary size :  102194

# 이제 각 문장을 숫자 벡터로 변환하여 encoding
data_train_seq = tokenizer.texts_to_sequences(data_train)
print(data_train_seq[:2])
# [[42, 428, 8, 6671, 635], [905, 430, 34, 576, 2, 183, 1547, 14, 957, 6109, 1]]

# 5. 동일한 문장 길이로 정리해야 합니다.
# 이 작업을 하기 위해 문장 최대 길이를 구해보죠.
max_length = max(len(x) for x in data_train_seq)
print('문장 최대 길이 : ', max_length)
# 문장 최대 길이 :  71

# 문장의 길이를 동일하게 맞추기 위해 pad_sequences 함수를 이용합니다. 
# 이때, 최대길이보다 짧은 문장은 문장 앞에 0을 붙여서 길이를 맞춥니다. 

from tensorflow.keras.preprocessing.sequence import pad_sequences

x_data_train = pad_sequences(data_train_seq,
                             truncating='post',
                             padding='pre',
                             maxlen=max_length)

t_data_train = np.asarray(train_df['label'])

print('샘플 형태 : ', x_data_train[:1])

~~~

### 모델 구현

`Embedding`은 tensorflow keras가 제공하는 `Embedding layer`를 이용해 구현합니다.
정수 인덱스에서 고밀도 vector로 매핑되어 단어 간의 유사성을 함께 인코딩하게 됩니다.
<br><br>

코드는 다음과 같습니다.

~~~python

import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Bidirectional, LSTM, Embedding
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping

model = Sequential()

# Embedding 파라미터
# input_dim : 단어 사전의 크기
# output_dim : 출력 vector의 차원
# input_length : 입력 길이
model.add(Embedding(input_dim=voca_size,
                    output_dim=32,
                    input_length=max_length))

model.add(Bidirectional(LSTM(units=16,
                             activation='tanh')))

model.add(Dense(units=32,
               activation='relu',
               kernel_initializer='he_normal'))

model.add(Dense(units=1,
                activation='sigmoid',
                kernel_initializer='he_normal'))

model.compile(optimizer=Adam(learning_rate=1e-4),
              loss='binary_crossentropy',
              metrics=['accuracy'])

# Checkpoint
checkpoint_cb = ModelCheckpoint(filepath='./best_model.ckpt',
                                save_weights_only=True,
                                save_best_only=True,
                                monitor='val_loss',
                                verbose=1)

# Early Stopping
earlystopping_cb = EarlyStopping(monitor='val_loss',
                                 patience=3)

# 학습
history = model.fit(x_data_train,
                    t_data_train,
                    epochs=10,
                    batch_size=64,
                    validation_split=0.2,
                    callbacks=[checkpoint_cb, earlystopping_cb],
                    verbose=1)

# Epoch 1/10
# 1874/1875 [============================>.] - ETA: 0s - loss: 0.5317 - accuracy: 0.7126
# Epoch 00001: val_loss improved from inf to 0.40060, saving model to .\best_model.ckpt
# 1875/1875 [==============================] - 41s 22ms/step - loss: 0.5317 - accuracy: 0.7127 - val_loss: 0.4006 - val_accuracy: 0.8205
# ...                
# Epoch 7/10
# 1873/1875 [============================>.] - ETA: 0s - loss: 0.2804 - accuracy: 0.8842
# Epoch 00007: val_loss did not improve from 0.34858
# 1875/1875 [==============================] - 39s 21ms/step - loss: 0.2804 - accuracy: 0.8842 - val_loss: 0.3587 - val_accuracy: 0.8453                

~~~

결과를 보면 training set의 accuracy는 약 88%이고 validation set의 accuracy는 84%입니다.
`history`객체를 이용해 결과 그래프를 그려보죠.

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
file='deep-learning-naver-review-loss_accuracy-graph.png'
%}

### 평가

모델의 구현이 다 되었으니 이제 test data set을 이용해서 우리 모델을 평가해보겠습니다.
test data를 loading하는 것부터 전처리를 거쳐 평가하는 것까지 다음의 코드로 표현될 수 있습니다.

~~~python


test_file = tf.keras.utils.get_file(
    './ratings_test.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_test.txt',
    extract=True
)

test_df = pd.read_csv(test_file, sep='\t')

# 1. 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
test_df['document'] = test_df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')

# 2. 결측지 제거 (5개)
test_df = test_df.dropna()

# 3. 불용어(stopword)제거

okt = Okt()

def word_tokenization(text):
    stop_words = ['는', '을', '를', '이', '가', '의', '던', '고', '하', '다', 
                  '은', '에', '들', '지', '게', '도']
    return [word for word in okt.morphs(text) if word not in stop_words]

data_test = test_df['document'].apply((lambda x:word_tokenization(x)))

# 4. 각 문장을 숫자 벡터로 변환하여 encoding
data_test_seq = tokenizer.texts_to_sequences(data_test)

# 5. 동일한 문장 길이로 정리해야 합니다.
x_data_test = pad_sequences(data_test_seq,
                            truncating='post',
                            padding='pre',
                            maxlen=max_length)

t_data_test = np.asarray(test_df['label'])

# evaluation
print(model.evaluate(x_data_test, t_data_test))

# 1563/1563 [==============================] - 6s 4ms/step - loss: 0.3694 - accuracy: 0.8386
# [0.36936047673225403, 0.8385753035545349]

~~~

정확도가 약 83%정도 나오는 군요. 어느 정도는 학습이 된 것을 확인할 수 있습니다.

### Review 데이터 Test

그럼 몇가지 Review를 직접 입력해서 감성분류가 잘 되는지 결과를 살펴보죠.

~~~python

review_sentences = ['내가 만들어도 이것보단 잘 만들겠다.',
                    '너무너무 재미있었습니다. 감사합니다.',
                    '아..내 돈... 돈 아까워 죽을거 같아요',
                    '아나..이것도 영화라고 만들었냐. 무슨 스토리가 산으로 가냐',
                    '감동과 재미가 같이 있는 영화입니다. 훌륭합니다.',
                    '너무너무 재미없다.. 잠와 죽는줄.',
                    '너무너무 재미있다.. 잠이 확깨네.']


df = pd.DataFrame({'document': review_sentences})


# 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
df['document'] = df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')

# 불용어(stopword)제거

okt = Okt()

def word_tokenization(text):
    stop_words = ['는', '을', '를', '이', '가', '의', '던', '고', '하', '다', 
                  '은', '에', '들', '지', '게', '도']
    return [word for word in okt.morphs(text) if word not in stop_words]

data_predict = df['document'].apply((lambda x:word_tokenization(x)))

# 4. 각 문장을 숫자 벡터로 변환하여 encoding
data_predict_seq = tokenizer.texts_to_sequences(data_predict)

# 5. 동일한 문장 길이로 정리해야 합니다.
x_data_predict = pad_sequences(data_predict_seq,
                               truncating='post',
                               padding='pre',
                               maxlen=max_length)

# predict
print(model.predict(x_data_predict))

# [[0.01470953]
#  [0.9794641 ]
#  [0.01646951]
#  [0.01636818]
#  [0.9903786 ]
#  [0.008248  ]
#  [0.37347972]]

~~~

### KoBERT tokenizer

`SK T-Brain`에서 한국어 성능 향상을 위해 개발된 `KoBERT`를 제공합니다. 이것을
이용하면 쉽게 한국어 사전에 학습된 모델과 토큰화를 활용할 수 있습니다. 우리의 예제의 순환신경망
모델은 그대로 활용하고 tokenizer만 `KoBERT`를 적용해 보겠습니다.
(즉, tokenizer만 가져다 사용하고 사전 학습된 KoBERT 모델을 직접 다루지는 않겠습니다. 따라서
성능의 직접적인 향상은 기대하기 힘들 듯 합니다. 단, 전이학습 방식으로 `KoBERT`를 사용한다면
성능 향상을 기대할 수 있습니다. )
<br><br>

이 부분은 `Google Colab`을 이용해야 합니다. 로컬 PC에서 작업할 경우 설치에 상당히 오랜 시간이
소요될 수 있기 때문입니다.

먼저 Google Colab에 `KoBERT`를 설치해야 합니다.

~~~python

!git clone https://github.com/SKTBrain/KoBERT.git

cd KoBERT

!pip install -r requirements.txt

~~~

설치가 완료된 후 아래의 코드를 이용해 정확도를 확인해보죠. 아마도 정확도는 크게
차이가 나지 않을 듯 합니다.

~~~python

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Bidirectional, LSTM, Embedding
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping
from gluonnlp.data import SentencepieceTokenizer
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from kobert.utils import get_tokenizer
import warnings

warnings.filterwarnings(action='ignore')

# Raw Data Loading
# Naver sentiment movie corpus V1.0 

train_file = tf.keras.utils.get_file(
    './ratings_train.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_train.txt',
    extract=True
)

train_df = pd.read_csv(train_file, sep='\t')

# 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
train_df['document'] = train_df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')

# 결측지 제거 (5개)
train_df = train_df.dropna()

# 불용어(stopword)제거
tok_path = get_tokenizer()
sp = SentencepieceTokenizer(tok_path)

def word_tokenization_kobert(text):
#     stop_words = ['는', '을', '를', '이', '가', '의', '던', '고', '하', '다', 
#                   '은', '에', '들', '지', '게', '도']
#     return [word for word in okt.morphs(text) if word not in stop_words]
    return [word for word in sp(text)]

data_train = train_df['document'].apply((lambda x:word_tokenization_kobert(x)))
print(data_train.head())

# 단어 사전을 생성합니다.
oov_token = '<OOV>'  # 사전에 없는 단어
voca_size = 15000  

tokenizer = Tokenizer(oov_token=oov_token, num_words=voca_size)
tokenizer.fit_on_texts(data_train)

# 각 문장을 숫자 벡터로 변환하여 encoding
data_train_seq = tokenizer.texts_to_sequences(data_train)

max_length = max(len(x) for x in data_train_seq)
print('문장 최대 길이 : ', max_length)

# 문장의 길이를 동일하게 맞추기 위해 pad_sequences 함수를 이용합니다.
x_data_train = pad_sequences(data_train_seq,
                             truncating='post',
                             padding='pre',
                             maxlen=max_length)

t_data_train = np.asarray(train_df['label'])

# model 생성
model = Sequential()

# Embedding 파라미터
# input_dim : 단어 사전의 크기
# output_dim : 출력 vector의 차원
# input_length : 입력 길이
model.add(Embedding(input_dim=voca_size,
                    output_dim=32,
                    input_length=max_length))

model.add(Bidirectional(LSTM(units=16,
                             activation='tanh')))

model.add(Dense(units=32,
                activation='relu',
                kernel_initializer='he_normal'))

model.add(Dense(units=1,
                activation='sigmoid',
                kernel_initializer='he_normal'))

model.compile(optimizer=Adam(learning_rate=1e-4),
              loss='binary_crossentropy',
              metrics=['accuracy'])

# Checkpoint
checkpoint_cb = ModelCheckpoint(filepath='./best_model.ckpt',
                                save_weights_only=True,
                                save_best_only=True,
                                monitor='val_loss',
                                verbose=1)

# Early Stopping
earlystopping_cb = EarlyStopping(monitor='val_loss',
                                 patience=3)

# 학습
history = model.fit(x_data_train,
                    t_data_train,
                    epochs=30,
                    batch_size=64,
                    validation_split=0.2,
                    callbacks=[checkpoint_cb, earlystopping_cb],
                    verbose=1)

# 결과 그래프
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

# Evaluation(평가)

test_file = tf.keras.utils.get_file(
    './ratings_test.txt',
    origin='https://raw.githubusercontent.com/e9t/nsmc/master/ratings_test.txt',
    extract=True
)

test_df = pd.read_csv(test_file, sep='\t')

# 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
test_df['document'] = test_df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')

# 결측지 제거 (5개)
test_df = test_df.dropna()

# 불용어(stopword)제거
data_test = test_df['document'].apply((lambda x:word_tokenization_kobert(x)))

# 각 문장을 숫자 벡터로 변환하여 encoding
data_test_seq = tokenizer.texts_to_sequences(data_test)

# 동일한 문장 길이로 정리해야 합니다.
x_data_test = pad_sequences(data_test_seq,
                            truncating='post',
                            padding='pre',
                            maxlen=max_length)

t_data_test = np.asarray(test_df['label'])

# 최적 모델 load
model.load_weights('./best_model.ckpt')

# evaluation
print(model.evaluate(x_data_test, t_data_test))
# [0.3965442478656769, 0.8243894577026367]

######################

# 사용자 정의 데이터를 이용한 prediction

review_sentences = ['내가 만들어도 이것보단 잘 만들겠다.',
                    '너무너무 재미있었습니다. 감사합니다.',
                    '아..내 돈... 돈 아까워 죽을거 같아요',
                    '아나..이것도 영화라고 만들었냐. 무슨 스토리가 산으로 가냐',
                    '감동과 재미가 같이 있는 영화입니다. 훌륭합니다.',
                    '너무너무 재미없다.. 잠와 죽는줄.',
                    '너무너무 재미있다.. 잠이 확깨네.']


df = pd.DataFrame({'document': review_sentences})

# 정규식을 활용해 영어, 한글, 띄어쓰기만 남기고 나머지 특수문자를 제거
df['document'] = df['document'].str.replace('[^A-Za-z가-힣ㄱ-ㅎㅏ-ㅣ ]', '')

# 불용어(stopword)제거
data_predict = df['document'].apply((lambda x:word_tokenization_kobert(x)))

# 각 문장을 숫자 벡터로 변환하여 encoding
data_predict_seq = tokenizer.texts_to_sequences(data_predict)

# 동일한 문장 길이로 정리해야 합니다.
x_data_predict = pad_sequences(data_predict_seq,
                               truncating='post',
                               padding='pre',
                               maxlen=max_length)

# predict
print(model.predict(x_data_predict))

# [[0.0729634 ]
#  [0.9926602 ]
#  [0.04697945]
#  [0.01277754]
#  [0.9929812 ]
#  [0.03266287]
#  [0.21954349]]

~~~


End.

{% include links.html %}
