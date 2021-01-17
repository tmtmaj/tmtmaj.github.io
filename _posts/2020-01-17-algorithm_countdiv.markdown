---
title: "﻿[Algorithm] CountDiv﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] [CountDiv]](https://app.codility.com/programmers/lessons/5-prefix_sums/count_div/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(A, B, K):
    # write your code in Python 3.6
    # print(A, B, K)

    if A %K != 0:
        if A > K:
            A += A %K
        else:
            A = K
    if B % K != 0:
        B -= B % K
    # print(A, B)
    return (B-A)//K +1
    pass
```

