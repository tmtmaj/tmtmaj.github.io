---
title: "﻿﻿﻿﻿[NLP] 얼라인먼트 정보로 Top-N 딕셔너리 추출 Extract Top-N dictionary from bilingual alignment﻿﻿"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---

﻿

안녕하세요. 



이번 포스팅에서 소개해드릴 내용은 다음과 같습니다.



### **얼라인먼트 정보로 Top-N 딕셔너리 추출 Extract Top-N dictionary from bilingual alignment**

------

앞선 포스팅에서 [bilingual dataset에서 alignment 를 추출하는 두가지 방법](https://jeonghyeokpark.netlify.app/natural-language-processing/2020/10/25/wordaligner.html)에 대해 설명드렸습니다.



이 포스팅에서 설명하려는 Top-N 딕셔너리를 추출하는 방법은 bilingual data 뿐만 아니라 그 data로부터 추출된 alignment 도 필요하니 위 포스팅에서 방법을 확인하시고 alignment를 추출해주세요.  



alignment 파일이 있다고 가정하고 설명 시작하겠습니다.

관련 코드는 제 [github repository](https://github.com/tmtmaj/Extract-dictionary-from-bilingual-alignment)에 올려놨습니다. 간단한 toy test도 준비해두었으니 사용법을 터득하기는 쉬우실 것 같습니다.

------

### How to extract Top-N dictionary from bilingual alignment?

Top-N dictionary는 다음과 같은 방식으로 추출됩니다.



1. source token에 대한 target token의 발생빈도를 딕셔너리의 형태로 추출합니다.

   ex: "좋아하다":{"like":68, "love":30, ...}

2. source token를 발생 빈도에 따라 정렬

   ex: "는":293045, "이":273045, "가":239485 ..

3. 유저가 설정한 N (출력된 dictionary의 크기, 가장 발생빈도가 높은 source token부터 차례로)과 n_best (대응되는 target의 수)대로 결과가 출력됩니다.

```
# N-4/n-best-2

defaultdict(None,
{'.': ['.'],
'이': ['this', 'were'],
'을': ['for', 'with'],
'에': ['on', 'in'],})﻿
```



추가적으로 sentence analyzer나 사용자 본인의 설정으로 큰 의미를 갖지 못하는 단어("은", "는"..)를 제거하시면 좀 더 유의미한 dictionary를 추출하실 수 있습니다.



감사합니다.