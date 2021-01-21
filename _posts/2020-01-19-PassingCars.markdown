---
title: "﻿[Algorithm] GenomicRangeQuery﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] [PassingCars]](https://app.codility.com/programmers/lessons/5-prefix_sums/passing_cars/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(A):
    # write your code in Python 3.6

    answer = 0
    zero_cnt = 0
    one_cnt = 1

    for i in A:
        if i == 0:
            zero_cnt +=1
        

        if i == 1:
            answer += zero_cnt * one_cnt
        
    if answer > 1000000000:
        return -1
    return answer
    pass
```

