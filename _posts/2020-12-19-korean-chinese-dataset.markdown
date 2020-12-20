---
title: "﻿﻿﻿﻿﻿[NLP] 한국어-중국어 병렬 코퍼스 Korean-Chinese parallel corpora ﻿"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---

﻿

안녕하세요. 



이전에 제 논문에서 사용된 한국어-중국어 병렬 코퍼스를 공개합니다.

------

### 한국어-중국어 병렬 코퍼스 Korean-Chinese parallel corpora



- #### News Korean-Chinese parallel corpus



첫번째 코퍼스는 Dong-A news에서 크롤링을 통해 수집한 데이터입니다.

대략 25만 문장 정도로 구성되어있습니다.

크롤링 방법 및 data preprocessing은 다음과 같습니다.

1. Dong-A news 홈페이지로부터 한국어 기사, 중국어 기사 추출 

2. 한국어: 한국어 문장 분리기 (pip install kss) 사용 

    중국어: 

   ```ABAP
   re.split('(。|！|\!|\.|？|\?)',paragraph) 
   ```

   

3. 각 기사의 분리된 한국어 문장과 분리된 중국어 문장의 갯수가 다르면 그 기사 제외

4. 문장 길이에 따라 조건에 부합하지 않는 문장 쌍은 제외

   조건: 한국어 문장이 중국어 문장보다 김. 한국어: 중국어 = 2:1 이상 / 한국어: 중국어 0.8:1 이하 제거

5. @메일 주소, 김** 기자 등 의미없는 문장 제거

6. 마침표, 따옴표 등 punctuation 통일



- #### Conversation Korean-Chinese parallel corpus



두번째 코퍼스는 SWRC (Kaist corpus)로부터 받은 데이터입니다.

약 5만 문장으로 구성되어 있습니다.

중국어 공부도 할겸.. 하나하나 보면서 이상한 문장 쌍은 전부 제거했습니다.

------

﻿데이터 셋은 제 [github repository](﻿https://github.com/tmtmaj/Korean-Chinese-parallel-dataset-for-machine-translation-task)에서 다운받을 수 있습니다.

﻿[관련 논문](﻿https://jeonghyeokpark.netlify.app/publication/)의 bibtex입니다.

```
@article{DBLP:journals/corr/abs-1911-11008,
  author    = {Jeonghyeok Park and
               Hai Zhao},
  title     = {Korean-to-Chinese Machine Translation using Chinese Character as Pivot
               Clue},
  journal   = {CoRR},
  volume    = {abs/1911.11008},
  year      = {2019},
  url       = {http://arxiv.org/abs/1911.11008},
  archivePrefix = {arXiv},
  eprint    = {1911.11008},
  timestamp = {Tue, 03 Dec 2019 14:15:54 +0100},
  biburl    = {https://dblp.org/rec/journals/corr/abs-1911-11008.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```



감사합니다.