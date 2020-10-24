---
title: "[NLP] ﻿자연어 데이터 처리를 위한 리눅스 명령어"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---


안녕하세요.

이번 포스팅에서 소개해드릴 내용은 데이터 처리을 위한 리눅스 명령어입니다.
인터넷에 "리눅스 명령어"를 쳐보니 필수 리눅스 명령어, 30가지 리눅스 명령어.. 등등 많은 자료들이 있었습니다.
하지만 정작 제가 필요한 기능에 대한 답은 찾기 어렵더군요..

그래서 기록도 할겸 필요하신 분들에게 알려드릴 겸 몇 가지 리눅스 명령어를 예시와 함께 적으려합니다.
파일 정리, 자연어 처리를 하시는 분들에게 유용할 거라 생각합니다.
(작성된 코드는 google colab에서 그대로 사용 가능합니다.)

사용될 예제 파일입니다.

korean.ori

```
강아지들과 내 사진을 보낼게.
그 수익금 중 일부를 위안부 할머니들을 위해 쓰고 그들을 위해 여러 가지 캠페인을 벌이고 있습니다.
그들은 내가 잘하는 것을 바탕으로 별명을 사용하고 있기 때문에 나는 사람들이 치타라고 불러주면 기분이 좋아.
그러므로 실제로 컴퓨터 프로그램을 만든 사람이 프로그램에 대한 저작자가 돼요.
나는 친구에게 그 철학자의 책을 선물해 주겠다고 말했습니다.
나머지 사진은 내 친구들이야.
나머지 시간에는 공부해요.
네가 하는 일과 공부 잘하길 멀리서 응원할게.
다른 선수들이 몬스터를 사냥할 경우 당신은 추가 경험치를 획득해요.
당신에게 영화관 티켓을 그냥 보여 주면 되나요?
```

english.ori

```
And I'll send you a picture of me and my dogs.
Part of profits are used for the comfort women, and it is holding various campaigns for them.
I feel happy when people call me cheetah because they are using a nickname based on something that I am good at.
So, a person who made a computer program actually becomes an author of that computer program.
I told my friends that I will give you the philosopher's book as a gift.
And the rest of the pictures are my friends.
I study for the rest of the time.
I will cheer you on your work and your grade from far away.
If other players hunt monsters, you gain additional experience.
Can I just show you my ticket to the movie theater?
```

아래 예제를 google colab에서 실행하시려면 File 목록에 위의 두 파일을 만들어주세요.

---

## 1. awk

### awk 의 기본 형식

```
$ awk [options] ﻿[program] [file]
```

#### [options]

- F fs 파일의 구분 문자
- f file program 파일을 지정
- v var=value program에서 사용될 variable 지정(여러개 지정가능)

#### [program]

program은 '/selection_criteria/ {action}'의 형식으로 사용됩니다.
작은 따옴표(') 안에서 작성되어야 합니다.
selection_criteria는 슬레시(/) 안에서 작성되어야 하고, pattern으로 통용되기도 합니다.
record와 selection_criteria이 일치하는 부분이 있으면 그 레코드에 대해서 action을 취합니다.

action은 중괄호({}) 안에서 작성되어야 합니다.
여기서 말하는 record는 완전한 하나의 line을 일컫습니다.
즉, line에서 pattern과 일치하는 부분이 있으면 line에 대해 action을 취하는 것이죠.
selection_criteria는 생략될 수 있습니다. 그럼 파일의 모든 line에 대해 action을 취하는 것입니다.
action도 생략될 수 있는데요. default 값은 {print $0}입니다.

record와 field는 awk program에서 사용할 수 있는 변수입니다.
행은 record이고 열은 field를 가르킵니다. 아래의 예시를 보시면 record는 한 line을 가르키고, field $1, $2, $3... $n 까지 각 문장들을 공백으로 구분시켰을 때, 각각 첫번째 열, 두번째 열, .. n번째 열을 가르킵니다.

	
![]({{ site.baseurl }}/assets/linuxfornlp1.png )

	

#### [file]

말 그대로 file을 지정하는 부분입니다.
형식은 input-file > output-file이고 output-file은 생략될 수 있습니다.

```
!awk '/강아지/ {print $0}' /content/korean.ori
# 강아지들과 내 사진을 보낼게.
```

위의 예시에서 /selection_criteria/는 /강아지/입니다.
'강아지'가 포함된 문장에 대해 action {print $0}을 하면 되는 것이죠.
'강아지'가 포함된 문장은 첫번째 문장 뿐이니 첫번째 문장의 $0(record 온전한 line)을 출력합니다.
따라서 출력 값은 '강아지들과 내 사진을 보낼게.'가 됩니다.

```
!awk '/나머지/ {print NR,$1,$3}' /content/korean.ori 
# 6 나머지 내
# 7 나머지 공부해요.
```

이번 예시의 /selection_criteria/는 /나머지/입니다.
'나머지'가 포함된 문장에 대해 action {print NR,$1,$3}을 하면 되는 것이죠.
NR은 line number를 의미합니다. 그리고 $1과 $3은 첫번째 필드와 세번째 필드를 의미하죠.
따라서 '나머지'가 포함된 문장의 line number, 첫번째 단어, 세번째 단어를 출력하는 것입니다.
'나머지'가 포함된 문장은 다음과 같고 각각 6, 7번째 줄입니다.
아래 문장에서 ``*``이 첫번째 field, ``**``이 세번째 field입니다.


나머지* 사진은 내** 친구들이야.  
나머지* 시간에는 공부해요.**

따라서 위와 같이 출력이 됩니다.
awk의 기능은 매우 다양합니다. 특히 ``[program]``에서 for, if, length 등 다양하게 활용할 수 있습니다.
전부 다 설명하기엔 너무 많아서 제가 자주 쓰는 기능 몇 가지에 대해 설명드리겠습니다. 예시와 함께요!

```
# split dataset into trainset, testset 
!awk '{if(rand()<0.9) {print > "/content/train.set"} else {print > "/content/test.set"}}' '/content/korean.ori'
```

awk를 이용하여 dataset을 trainset과 testset으로 분리할 수 있습니다.
일단 /selection_criteria/이 없음으로 모든 라인에 대해서   
``action {if(rand()<0.9) {print > "/content/train.set"} else {print > "/content/test.set"}}``  
을 수행합니다.
꽤 길죠. 하지만 별거 없습니다.
간단한 if문입니다.

if문의 조건은 rand() < 0.9인데, rand()은 0과 1 사이의 값을 랜덤으로 생성합니다.
그렇다면 이 if문은 생성된 random 값이 0.9보다 작으면 record(한 문장)을 "/content/train.set"에 저장하고,
크다면 "/content/test.set"에 저장하라는 의미입니다.
( 참고로 ``>`` 표시는 overwriting의 의미입니다. 만약 해당 파일이 존재하지 않는 다면 새로 생성하죠. 그리고 ``>>`` 표시는 덧붙여 쓰는 것입니다. )
즉, 이 awk 명령은 korean.ori 파일을 90:10의 퍼센테이지로 나누어 train.set과 test.set을 생성하라는 의미입니다.

```
# split dataset into trainset, testset
!awk -v train="/content/train.set" -v test="/content/test.set" '{if(rand()<0.9) {print > train} else {print > test}}' '/content/korean.ori'
```

바로 위의 awk 명령과 같은 동작을 수행합니다. 단지 option 중 하나인 -v를 추가하여 ``[program]``의 가독성을 높혔습니다.

---

## 2. cat

가장 자주 쓰이는 명령어 중 하나입니다.
Concatenate를 의미하는 명령어인데요. 파일을 이어붙이는 데에서 유래하지 않았나 싶네요.
파일을 읽고 출력하는 명령어입니다.

```
# korean.ori 파일 2개 이어붙이기
!cat /content/korean.ori /content/korean.ori
```

korean.ori 파일의 내용이 두 번 출력되는 것을 확인할 수 있습니다.  
출력을 저장하고 싶으시다면 '> /content/korean_twice.ori'를 뒤에 덧붙이시면 됩니다.

```
# 현재 디렉토리의 .ori 파일 모두 출력
!cat *.ori
# korean.ori 파일의 공백 라인을 모두 지움
!cat -s /content/korean.ori
# korean.ori 파일의 내용을 korean2.ori로 복사
!cat /content/korean.ori > /content/korean.ori
# 줄 넘버와 함께 출력
!cat -n /content/korean.ori
```

cat을 활용한 간단한 예시입니다.
제가 cat을 가장 많은 활용하는 경우는 파일의 내용을 넘겨줄 때입니다.

```
# cat으로 입력 파일을 받아 print_line.py를 실행
!cat /content/korean.ori | python /content/print_line.py > /content/korean.print

# print_line.py (문장을 입력 받아 출력하는 python 파일)

import sys

for line in sys.stdin.readlines():
    line = line.rstrip('\n')
    print(line)
```

위에 예시에서 보시면 cat으로 korean.ori 파일을 받아 ``|`` (수직선, vertical bar)을 통해 python으로 넘겨주는 과정을 확인할 수 있습니다.
print_line.py는 단순히 입력된 문장을 그대로 print하는 내용이라 별 특별한 것은 없습니다.
여기서 중요한 것은 ``|`` 인데요.
리눅스나 유닉스 시스템에서는 ``|``가 pipe 역할을 수행합니다. 그럼 pipe가 뭐냐? 한 프로그램의 output을 다른 program으로 넘겨주는 것(redirection)입니다.
즉, cat에서 처리한 output을 python의 input으로 넘겨주는 것을 의미하죠.

```
# 파일에 문장 갯수를 출력
!cat /content/korean.ori | wc -l
```

wc는 파일의 line, word, char, byte을 count할 수 있는 명령어입니다.
cat으로 파일을 받아 wc 명령으로 넘겨주어 파일의 문장 갯수를 출력합니다.
wc도 유용하게 쓰이는 데요.\\
5가지 options이 있습니다.
- -l 문장 갯수 출력
- -w 단어 갯수 출력
- -m 캐릭터 갯수 출력
- -c byte 갯수 출력
- -L 가장 긴 문장의 길이 출력

---

## 3. paste

paste 명령어는 말 그대로 input file의 모든 라인을 이어 붙이는 것입니다.

```
# korean.ori와 english.ori의 각 라인을 이어붙이고 출력
!paste /content/korean.ori  /content/english.ori 
# 강아지들과 내 사진을 보낼게.	And I'll send you a picture of me and my dogs.
# 그 수익금 중 일부를 위안부 할머니들을 위해 쓰고 그들을 위해 여러 가지 캠페인을 벌이고 있습니다.	Part of profits are used for the comfort women, and it is holding various campaigns for them.
# 그들은 내가 잘하는 것을 바탕으로 별명을 사용하고 있기 때문에 나는 사람들이 치타라고 불러주면 기분이 좋아.	I feel happy when people call me cheetah because they are using a nickname based on something that I am good at.
# .
# .
```

github에서 문장을 처리하는 도구 중에 source와 target이 이어붙여진 input을 요구하는 경우가 종종 있기 때문에 아주 유용하게 쓰입니다.

```
# korean.ori와 english.ori의 각 라인을 구분자(delimiter) '_'를 사이에 두고 이어붙이고 저장 
!paste -d '_' /content/korean.ori  /content/english.ori > /content/ko_en.ori
# 강아지들과 내 사진을 보낼게._And I'll send you a picture of me and my dogs.
# 그 수익금 중 일부를 위안부 할머니들을 위해 쓰고 그들을 위해 여러 가지 캠페인을 벌이고 있습니다._Part of profits are used for the comfort women, and it is holding various campaigns for them.
# 그들은 내가 잘하는 것을 바탕으로 별명을 사용하고 있기 때문에 나는 사람들이 치타라고 불러주면 기분이 좋아._I feel happy when people call me cheetah because they are using a nickname based on something that I am good at.
# .
# .
```

문장 사이에 구분자를 넣고 싶으시다면 ``option -d``를 사용하시면 됩니다.
만약 3개 파일을 이어붙이고 구분자를 다르게 하고 싶다면 ``-d '_:'``라고 입력하시면 첫번째 문장과 두번째 문장 사이의 구분자는 ``_``으로 두번째 문장과 세번째 문장의 구분자는 ``:``으로 출력됩니다.

---

## 4. sed

sed 명령어는 awk 명령어와 마찬가지로 매우 다양한 기능을 수행할 수 있습니다.  
형식은 sed ``[options]`` ``[script]`` ``[file]`` 입니다.  
제가 자주 사용하는 기능 몇가지만 소개해드리겠습니다.

```
# korean.ori에서 '나머지'를 'namegi'로 변경
!sed 's/나머지/namegi/g' /content/korean.ori
# ..
# namegi 사진은 내 친구들이야.
# namegi 시간에는 공부해요.
# ..
```

script에서 s는 substitution을 의미하고 g는 전체 문장을 대상으로 한다는 것을 의미합니다.

```
# korean.ori에서 '나머지'가 포함된 문장 제거
!sed '/나머지/d' /content/korean.ori
```

script에서 /나머지/는 pattern이고 d는 delete의 약자입니다.

```
# korean.ori에서 5번째 줄부터 마지막 줄까지 출력
!sed -n '5,$p' /content/korean.ori
```

sed의 srcipt에서 ``$``은 마지막 line을 의미합니다. 반대로 첫번째 line은 ``^``입니다.

---

## Data preprocessing for mahicne translation using linux command 

위에 소개해드릴 명령어를 이용하여 기계번역에 필요한 데이터를 처리하는 과정을 설명해볼게요.

데이터 전처리 과정은 다음과 같습니다.

1. raw data tokenization (데이터 토큰화)
2. data split (trainset, testset을 나눔)
3. byte-pair encoding 적용

#### 1. raw data tokenization (데이터 토큰화)

한국어는 open-korean-text toolkit(okt), 영어는 moses를 이용하여 tokenization을 진행합니다.
먼저 moses와 konlpy를 설치합니다.

```
# moses 설치
!git clone https://github.com/moses-smt/mosesdecoder.git
# konlpy 설치
!pip install konlpy

# korean.ori에 토큰화하여 korean.tok에 저장
!cat /content/korean.ori | python /content/korean_tokenizer.py > /content/korean.tok
```

cat 명령어를 통해 korean.ori 파일을 python 명령으로 넘겨주고 ``>``를 통해 파일을 저장합니다.
okt tokenization을 커맨드 창에서 직접 이용할 수 있는 instruction이 없어서 python 파일을 통해 tokenization을 수행합니다.   
korean_tokenizer.py은 다음과 같습니다.
```
# korean_tokenizer.py
import sys
from konlpy.tag import Okt

okt = Okt()

for line in sys.stdin.readlines():
    line = line.rstrip('\n')
    print(' '.join(okt.morphs(line)))
```
```
# english.ori를 토큰화하여 english.tok에 저장
!cat /content/english.ori | perl mosesdecoder/scripts/tokenizer/tokenizer.perl -threads 8 -a -l en > /content/english.tok
```
perl 명령어를 통해 moses tokenizer를 수행할 수 있습니다.
```
# 내용 확인
!head -3 /content/korean.tok
# 강아지 들 과 내 사진 을 보낼게 .
# 그 수익금 중 일부 를 위안부 할머니 들 을 위해 쓰고 그 들 을 위해 여러 가지 캠페인 을 벌이 고 있습니다 .
# 그 들 은 내 가 잘 하는 것 을 바탕 으로 별명 을 사용 하고 있기 때문 에 나 는 사람 들 이 치타 라고 불러주면 기분 이 좋아 .
!tail -4 /content/english.tok
# I study for the rest of the time .
# I will cheer you on your work and your grade from far away .
# If other players hunt monsters , you gain additional experience .
# Can I just show you my ticket to the movie theater ?
```
tokenization이 잘 수행되었는지 head와 tail 명령어로 확인할 수 있습니다.
head -3은 파일의 첫 머리부터 세 문장을 출력하는 명령이고 tail -4는 파일 마지막부터 네 문장을 출력하는 명령입니다.

#### 2. data split (trainset, testset을 나눔)

현재 korean.tok과 english.tok이 생성되어있습니다.
만일 없다면 1번 과정이 제대로 수행되지 않은 것이니 다시 한번 확인 부탁드립니다.

데이터를 그대로 split 해도 괜찮지만 섞어주는 것이 더 좋습니다. 왜냐하면 대부분의 bilingual corpus는 비슷한 문장들이 밀집되어 있기 때문입니다. 따라서 먼저 korean.tok과 english.tok 파일을 shuffle하겠습니다.
english.tok과 korean.tok을 각각 shuffle하면 두 파일의 각 문장들이 대응되지 않을 수 있습니다.
따라서 paste 명령으로 두 파일을 이어붙인 후에 shuffle을 하고 다시 떨어뜨리는 식으로 수행합니다.
```
# 두 파일을 이어붙임 -> shuffle(순서를 섞음) -> 두 파일을 분리함
!paste -d : /content/korean.tok /content/english.tok | shuf | awk -v FS=":" '{ print $1 > "korean.shuf" ; print $2 > "english.shuf" }'
```
paste 명령어를 이용하여 두 파일을 이어붙입니다. 구분자는 ``:`` 입니다.
그리고 ``|``는 pipe죠. output 파일을 넘겨줍니다. shuf은 파일의 line을 섞어주는 명령어입니다.
다음 awk 명령을 수행합니다. split의 구분자는 ``:``이고 split 후 첫번째 필드는 korean.shuf로 두번째 필드의 내용은 english.shuf로 저장합니다.
다른 방법으로 같은 과정을 수행할 수 있습니다.

```
# random seed 설정 -> 각 파일에 대해 shuf 명령 수행 
!dd if=/dev/random of=myrand count=1024
!shuf --random-source=myrand /content/korean.tok > /content/korean.shuf
!shuf --random-source=myrand /content/english.tok > /content/english.shuf
```

바로 shuf 명령의 random_seed를 똑같이 설정한 korean.tok과 english.tok에 대해 각각 shuf을 진행하는 것입니다. python, java, c 등의 언어의 shuffle 명령에서 seed를 설정하면 섞고 난 결과가 항상 같습니다.
shuf 명령도 마찬가지입니다. 먼저 random seed를 생성하고 shuf 명령에 random seed를 주고 shuffle을 수행하면 두 파일이 똑같은 순서로 섞여진 것을 확인할 수 있습니다.
다음은 순서가 섞인 파일을 trainset과 testset으로 split하는 것입니다.
```
# split into trainset and testset
!awk -v train="/content/train.ko" -v test="/content/test.ko" '{if(rand()<0.9) {print > train} else {print > test}}' '/content/korean.shuf'
!awk -v train="/content/train.en" -v test="/content/test.en" '{if(rand()<0.9) {print > train} else {print > test}}' '/content/english.shuf'
```
위에 awk 명령에서 설명해드렸던 예제이니 다시 설명하지는 않겠습니다.
이 명령을 수행하고 나면 train.ko, test.ko, train.en, train.en 파일이 생성됩니다.

#### 3. byte-pair encoding 적용
```
# subword-nmt 설치
!git clone https://github.com/rsennrich/subword-nmt.git
# bpe model 학습
!python /content/subword-nmt/subword_nmt/learn_bpe.py -s 20 < /content/train.ko > /content/korean.bpe
!python /content/subword-nmt/subword_nmt/learn_bpe.py -s 20 < /content/train.en > /content/english.bpe
# bpe model 적용
!python /content/subword-nmt/subword_nmt/apply_bpe.py -c /content/korean.bpe < /content/train.ko > /content/train.ko.bpe
!python /content/subword-nmt/subword_nmt/apply_bpe.py -c /content/korean.bpe < /content/test.ko > /content/test.ko.bpe
!python /content/subword-nmt/subword_nmt/apply_bpe.py -c /content/english.bpe < /content/train.en > /content/train.en.bpe
!python /content/subword-nmt/subword_nmt/apply_bpe.py -c /content/english.bpe < /content/test.en > /content/train.en.bpe
```
trainset에 대해서 bpe 모델을 학습한 후 trainset과 testset에 적용시켜주면 됩니다.
여기까지 하시면 기계번역을 위한 데이터 전처리는 끝납니다.

---

## 고찰

여기까지가 명령어 소개였고, 이 밑으로는 이 포스팅의 주제에 대한 탈고하지 않은 제 작은 고찰을 적어봤습니다.
저는 현재 자연어 처리를 연구하고 있는 학생입니다. 그 중에서도 기계번역 관련한 공부를 하고 있습니다.

데이터를 가지고 모델을 훈련을 시키는 것도 중요하지만 데이터를 전처리하는 과정이 더욱 중요하다고 생각합니다. 실제로 어떻게 데이터 전처리에 따라서 성능이 크게 차이가 납니다. 
일반적으로 law data를 그대로 사용할 수 없기 때문에 데이터를 모델에 훈련시키기 전에 데이터를 가공하는 과정이 필요합니다. (dataset preprocessing)

사실 데이터 크기가 그렇게 크지 않다면 Notepad++ 등의 편집기를 사용하거나 jupyter notebook으로 파일을 불러와 여러 처리를 할 수 있죠.
텍스트 편집기, python을 이용한 데이터 전처리도 좋지만, 데이터 전처리를 위한 bash 파일을 만드는 것이 더 좋은 방법 같다고 생각합니다. (위에 설명드린 리눅스 명령어들을 모아 bash 파일 만들고, bash 파일을 돌리면 전처리 과정이 쭉 진행됩니다.)
jupyter notebook으로 전처리를 하면 다시 그 파일을 이용하기 어렵더라구요. 잘 정리해놓지 못한 제 탓도 있지만..

예전에 Unsuperviced Machine Translation(비지도 기계 번역)에 관해 연구할 때는 한국어 문장 500백만, 중국어 문장 500백만 정도를 사용했는데요. 사실 500만 줄의 데이터도 비지도 기계 번역 학습에는 턱없이 부족한 양입니다. 이 정도 크기면 편집기가 감당을 못해서 '응답없음'이란 메시지와 함께 혈압이 오르죠.
저는 Window가 익숙하고 Window로 모든 프로그램, github에 존재하는 모든 코드 다 돌릴 수 있을 거야라고 생각했습니다. 시간과 노력만 있으면 가능합니다. 친절하신 분들이 다른 언어의 코드를 python 환경에서도 돌릴 수 있도록 제공해주신다면.. 물론, 가능합니다. 제가 python으로만 기계 학습을 해서 python을 예로 들었습니다.

예를 들어, 한국어 데이터 셋을 BPE(Byte-Pair Encoding)으로 인코딩해라!
이전의 저라면 jupyter notebook을 열고, 아래 코드에는 없지만 원래 데이터 셋 파일 열고, 다시 저장하는 과정까지.. 지금 생각하면 매우 귀찮은 과정입니다.

```
import sentencepiece as spm

spm.SentencePieceTrainer.train(input='input_data.txt',\
 model_prefix='bpe', vocab_size=1000,\
 user_defined_symbols=['foo', 'bar']) # bpe model training

sp = spm.SentencePieceProcessor()
sp.Load("bpe.model") # train된 모델을 load
sp.EncodeAsPieces("안녕하세요.")
# 안녕_ 하세요_ .
```

이외에도 다양한 애로사항이 있죠. 위 코드에서 사용한 sentencepiece는 다행히도 python module을 제공합니다. 아닌 경우는 정말 답이 없습니다..
하지만 저는 리눅스 버전의 instruction이 있어도 혹시 설치 과정에서 에러가 나면 어떡하지? input은 어떻게 넣지?등의 여러 걱정으로 python module만 찾아다니는 쫄보였습니다..

설치 튜토리얼에 bash, make, sudo.. 이런 것들을 보면 약간 거부감이 일더라구요.. (설치 과정에서 어디서 발생한 오류인지 모르겠지만, 아무리 구글링해도 찾을 수 없는 에러 때문에 컴퓨터를 싹 다 민 기억이 여러번 있어서 그런것 같습니다.)
하지만 요즘은 연구하기에 정말 좋은 환경이 많습니다.
바로 google colab, google cloud platform, Docker 등등..

내 컴퓨터에 뭘 설치할 필요가 없죠. 자원 사용 제한이 있긴 하지만요.
말씀 드린 플랫폼들에서 사용하는 가상 환경의 operating system은 대부분 리눅스 계열입니다.
google cloud platform은 선택할 수 있었던 것 같은데, 저는 데비안, 우분투만 썼습니다. 역시 리눅스 계열이죠.

리눅스 계열의 시스템에서는 bash 파일을 통해 BPE 처리 과정을 간단히 해결할 수 있습니다.

```
# bytepairencoding.sh
spm_train --input=<input> --model_prefix=<model_name> --vocab_size=8000 --character_coverage=1.0 --model_type=<type>
spm_encode --model=<model_file> --output_format=piece < input > output
```

``<input>`` ``<model_name>`` 등에는 본인의 하고 싶은 설정을 넣으시면 됩니다.
사실 처음 이 코드를 접하면 직관적이지 않을 겁니다. (처음 접하시는 거라면 jupyter notebook에서 한줄 한줄 테스트하고 예시를 들어가며 이해하는 것이 더 좋을 수 있습니다.)  
google colab에서 다음과 같이 사용할 수 있습니다.

```
# 설치 과정
!sudo apt-get install cmake build-essential pkg-config libgoogle-perftools-dev
!git clone https://github.com/google/sentencepiece.git
%cd /content/sentencepiece
!mkdir build
%cd build
!cmake ..
!make -j $(nproc)
!sudo make install
!sudo ldconfig -v

# 훈련 & 인코딩
# korean_data.txt를 bpe 8000으로 인코딩합니다.
spm_train --input=/content/korean_data.txt --model_prefix=korean_bpe_8000 --vocab_size=8000 --character_coverage=1.0 --model_type=bpe
spm_encode --model=korean_bpe_8000.model --output_format=piece < /content/korean_data.txt > /content/korean_bpe_output.txt
```

google colab은 jupyter notebook과 같기 때문에 python module을 import하고 encoding을 해도 괜찮습니다. 하지만 코드가 길어지고.. 귀찮습니다.
왜 리눅스 명령어를 익히면 왜 좋은지에 대해 말씀드렸습니다.  
정리하자면

1. github에서 배포된 코드가 python module 등 본인이 사용하는 언어로 wrapping되지 않은 경우가 많다.
2. python module이 있더라도 배포자가 제공하는 instruction보다 생산성이 떨어진다. (하나하나 체크하며 공부하기에는 python module을 사용하는 것이 더 좋다.)
3. 현재 이용하기 좋은 플랫폼(google colab 등)의 operating system은 대부분 리눅스 계열이다.
4. 방대한 양의 데이터를 처리할 경우(자연어 처리), 텍스트 편집기가 감당할 수 없다.

이 정도가 되겠네요.

당장 외우실 필요는 없고, 어떤 기능들이 있는지 알고 계셨다가 나중에 아! 그런 기능 있었던거 같은데 정도만 떠올리셔도 금방 찾아보실 수 있으니까요. 

그리고 google colab에 대해 몇가지 말씀드리고 싶은게 있어서 좀 더 적어보겠습니다.
저는 원래 컴퓨테에 달린 1080ti나 학교 연구실 서버에서 모델을 훈련시켰는데.. GPU 자원이 너무 부족하고 집에서 돌려놓으면 너무 뜨거워서..여름에는 특히나 힘들어요
그래서 최근에는 google colab를 이용하고 있습니다. 물론, Pro 버전을 구매했습니다 ㅎㅎ (google colab Pro도 GPU 사용에 제한이 있지만 무료 버전보다는 훨씬 좋더라구요. High-RAM도 사용할 수 있고) 한 달에 9.9달러에 고성능 GPU를 사용할 수 있습니다. 강추합니다. 저는 구글 아이디 2개로 전부 Pro를 구매해서 이용하고 있는데요. 한 아이디 당 최대 4개 세션 밖에 못 돌리더라구요. 그럼 총 8개를 돌리고 있는데, google chrome에서 하나, fire fox에서 하나 이렇게 돌리고 있습니다. 컴퓨터 램 크기가 16G밖에 안되서 그런지 훈련 과정에서 output이 많으면 컴퓨터가 자주 멈추더라구요.. 아마 램도 구매를 해야하지 않을까 생각합니다.
아마 머신러닝을 공부하시려는 분들은 google colab 사용해보거나 들어보셨을 거라 생각합니다. 한달에 만원 좀 넘는 가격 (사실 구글 드라이브도 좀 사야해서 한 3만원 정도)로 Tesla P100을 4개나 이용할 수 있습니다. 처음 이용하시는 분들은 먼저 무료 버전 사용해보시길 추천드립니다. google drive도 15G 무료로 제공하니까 mnist나 pytorch, tensorflow에 있는 튜토리얼 돌려보기에 충분할 것 같아요. 좀 더 큰 GPU 자원으로 훈련을 하고 싶다면.. Pro 구매하시길 전혀 손해가 아닙니다.

이번 포스팅은 여기까지입니다. 감사합니다.

참고한 사이트입니다.

https://konlpy.org/en/latest/

https://www.geeksforgeeks.org/



