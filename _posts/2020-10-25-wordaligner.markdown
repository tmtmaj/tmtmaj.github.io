---
title: "﻿[NLP] 비지도 워드 얼라이너 설치 및 사용법(Unsupervised Word Aligner Install and Use)"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---

﻿

안녕하세요.

이번 포스팅에서 소개해드릴 것은 word aligner입니다.
자연어 처리를 공부하고 계신 분들이라면 한번 쯤 들어보셨을 텐데요.
간단히 예를 들면,

["나는", "너를", "사랑해"]  
["I", "love", "you"]

위와 같이 서로 대응되는 문장이 있을 때, "나는"은 "I"에 대응되고, "너를"은 "you"에 대응되고, "사랑해"는 "love"에 대응되죠? (문장을 간단하게 공백으로 토큰화했습니다.)
각 단어에 대응되는 정보를 담고 있는 것이 word alignment입니다.
다른 언어 간에는 어순이 다르고 한 단어가 여러 단어에 대응될 수도 있으며, 혹은 대응되는 단어가 아예 없는 경우도 존재합니다.

이번 포스팅에서는 이러한 대응 관계를 학습하고 결과를 출력해내는 두 가지 방법에 대해 설치와 사용법에 대해 알려드리려고 합니다.
소개해드리기 전에 word alignment를 표현하는 방법에 대해 먼저 알려드리겠습니다.

word alignment를 표현하는 대표적인 방법은 "Pharaoh format"입니다.
위의 예시에 대한 word alignment를 Pharaoh format으로 표현하면,

한국어 to 영어

0-0 1-2 2-1

왜냐하면,

0(나는)-0(I) 1(너를)-2(you) 2(사랑해)-1(love)

각 단어에 대해 0부터 시작하는 index를 부여한 후, [source_language_index-target_language_index]의 방식으로 나열합니다.
반대로 영어 to 한국어라면

0-0 2-1 1-2

가 됩니다.

소개해드릴 두 방법의 출력 표현식 역시 Pharaoh format입니다.

### 1. fast align

github에서 보시면, fast_align is a simple, fast, unsupervised word aligner. 라고 소개합니다.

두번쨰 방법에 비하면 매우 빠릅니다. 40만 문장으로 이루어진 코퍼스라면 한 1시간 내외로 완료됩니다.
하지만 코퍼스의 크기가 크면 역시나 느립니다..

google colab에서 설치하고 사용하는 방법에 대한 instuction이 없어 설치에서부터 굉장히 애를 먹었던 기억이 있습니다.

```
%cd /content/
!git clone https://github.com/clab/fast_align.git
!sudo apt-get install libgoogle-perftools-dev libsparsehash-dev
%cd fast_align
!mkdir build
%cd build
!cmake ..
!make
%cd /content/
```

위의 코드를 입력해주시면 github에서 fast_align을 clone하고 설치를 전부 마칩니다.

사용 방법은 비교적 간단한 편입니다.
만약 한국어-영어 코퍼스를 가지고 있다면, 대부분 한국어 코퍼스, 영어 코퍼스 이렇게 나누어져 있을 텐데요.
약간의 전처리 과정이 필요합니다.

fast_align 모델은

"토큰화된 한국어 문장" ||| "토큰화된 영어 문장"

즉,

나는 너를 사랑해 ||| I love you

가운데 ``|||`` 막대 기호를 기준으로 한국어 문장과 영어 문장이 한 라인에 입력된 파일이 필요합니다.

전처리를 위한 리눅스 명령어입니다.
```
!paste -d "|" source_file target_file | sed 's/|/ ||| /g' > source_target_file
```
source_file에 source language corpus를 넣으시구요. target_file은 target language corpus를 넣으시면 source 문장과 target 문장이 이쁘게 합쳐져서 source_target_file로 출력됩니다.

(주의하실 점은 source language corpus나 target language corpus에 막대기호(|)가 있는지 없는지 확인해주세요. 있다면 삭제를 해주세요. 왜나면 위의 리눅스 명령어가 먼저 막대기호 하나를 사이에 두고 두 코퍼스를 합친 후에 막대기호 하나를 세 개로 바꾸는 명령어이기 때문입니다. 만약 원래 코퍼스에 막대기호가 있었다면 그 문장에는 두 개 이상의 삼 막대기호(|||)가 생겨서 정확한 alignment를 얻을 수 없습니다. )

```
!/content/fast_align/build/fastalign -i source_target_file -d -o -v > source_target_file.align

```
생성된 source_target_file을 입력으로 하여 source_target_file.align 파일을 생성할 수 있습니다.
생성된 파일은 source-to-target 방향의 alignment 입니다.
역방향의 alignment를 구하고 싶다면 -r을 추가해주세요.

```
!/content/fast_align/build/fastalign -i source_target_file -d -o -v -r > source_target_file.reverse.align
```

### 2. giza++

다음은 moses 라이브러리에서 제공하는 giza++입니다.
정말 설치부터 너무 힘들었던 기억이 납니다.
성능 면에서는 fastalign보다 더 좋은 성능을 냅니다.

설치



```
%cd /content/
!git clone https://github.com/moses-smt/mgiza.git
%cd mgiza/mgizapp
!cmake .
!make
!make install

%cd /content
!wget http://downloads.sourceforge.net/project/boost/boost/1.60.0/boost_1_60_0.tar.gz
!tar zxvf boost_1_60_0.tar.gz
%cd boost_1_60_0/
!./bootstrap.sh
!./b2 -j4 --prefix=$PWD --libdir=$PWD/lib64 --layout=system link=static install || echo FAILURE
%cd /content/
```



```
# /content/mgiza/mgizapp/manual-compile/complie.sh 변경
# SRC_DIR=/content/mgiza/mgizapp/src
# BOOST_ROOT=/content/boost_1_60_0
# BOOST_INCLUDE=$BOOST_ROOT/include
# BOOST_LIBRARYDIR=$BOOST_ROOT/lib64

!bash /content/mgiza/mgizapp/manual-compile/compile.sh
```



코드를 두 부분으로 나눈 것에도 의미가 있습니다.
설치하실 때 notebook에서 두 블락으로 나누어 하는 것이 편합니다.
왜냐하면, complie.sh을 변경해주어야 하기 때문입니다.

``/content/mgiza/mgizapp/manual-compile/complie.sh``를 열어 위에 부분과 대응되는 부분이 있습니다.
만약 경로가 같으면 그대로 사용하시면 되지만 다르다면 변경해주세요.

먼저 첫번쨰 코드는 giza++를 clone하고 설치, 그리고 boost_1_60_0을 설치합니다.
두번째 코드는 먼저 complie.sh 파일의 4줄을 위의 코드처럼 수정해주시고 run해주세요.
그러면 설치는 완료됩니다.

사용법 또한 굉장히 복잡합니다.
어떤 모델을 사용할지 일일히 커스텀하는 부분이 있는데, 저는 아래의 사이트를 참고했습니다.
제가 alignment에 대해 연구하는 동안 아주 유용하게 썼던 오픈 소스입니다.

https://github.com/lilt/alignment-scripts

run_giza.py와 giza.py의 경로만 설정해주시면 됩니다.

### 고찰

word alignment와 관련되어 몇 가지 고찰을 하자면..
통계적 기계 번역 모델(statistical machine translation)에서 word alignment는 굉장히 중요한 요소로 작용했습니다.
왜냐하면, 룰에 입각한 모델은 각 단어들을 미리 만들어 놓은 dictionary에 맞게 변경하고 나서 어순에 따라 조정해야하기 때문입니다.
하지만, 현재 성행하는(?) neural machine translation에서 사실 word alignment 모델이나 두 언어 사이의 alignment 결과를 이용하는 경우는 거의 드뭅니다.
attention 모델이 그 역할을 대신하는 경우가 많기 때문입니다.

하지만 최근에 관련된 모델을 연구하면서 느낀 점은 neural machine translation model도 충분히 잘 해주고 있지만 alignment 정보를 추가하면 더 좋은 성능을 내지 않을까하는 생각이 들었습니다.
여러 논문에서 번역 모델에서 가장 많이 활용되는 transformer에 대해 분석하였는데, 공통적인 의견은 attention head가 가진alignment 정보의 질이 fastalign, giza++보다 떨어진다는 것입니다.
(attention head에서 어떻게 alignment 정보를 추출했느냐에 따라 alignment 정보가 다소 손실될 수 있지만)

관련 연구를 찾아보니 한 attention head를 fastalign, giza++의 aligment 정보로 강제하여 alignment를 학습하도록 하는 연구도 있었지만, translation 성능을 향상시키지는 못했습니다.
MT decoding을 가이드 하는 방식으로 test set에서 dictionary를 뽑아내어 해당하는 source word를 무조건 매칭되는 target word로 번역하는 방법은 존재합니다.
BLEU score를 평가 메트릭으로 사용하였을 때, 최대 4 정도 향상되는 것을 확인할 수 있는데, 생각해보면 활용 범위가 넓은 것이 아닙니다. (test set은 미지의 영역인데 test set으로부터 정보를 얻는 것이니까요)
예를 들어, 사용자가 어떤 단어에 대해 특정 번역을 사용할 때 유용할 수 있겠죠.
'강력한'이라는 단어를 영어로 번역하면 'strong', 'powerful' 정도로 번역할 수 있겠죠. 영어 네이티브나 정통하신 분은 문맥에 따라 이 두 단어를 구분하실 지도 모릅니다. 
유저가 '강력한'을 무조건 'powerful'로 번역하도록 하는 번역기를 사용하고 싶다면 유용하다고 말할 수 있습니다.

또 다른 응용으로, 문장 요약에 관련된 연구 중 attention head의 alignment 정보로 contrastive attention을 만들어 성능을 꽤나 높힌 것도 있었습니다.
이런 저런 연구들을 참고하여 저도 여러 시도를 해봤지만, machine translation task에 대해서는 별다른 소득이 없었습니다.

neural machine translation에서 word alignment 정보를 아름답게 추가할 방법은 없는지...
고민이 많아지는 하루입니다.

감사합니다!
﻿
참고한 사이트입니다.

https://hovinh.github.io/blog/2016-01-27-install-giza-ubuntu/