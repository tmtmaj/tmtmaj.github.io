---
title: "﻿[Algorithm] MissingInterger﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] MissingInterger](https://app.codility.com/programmers/lessons/4-counting_elements/missing_integer/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(A):
    # write your code in Python 3.6
    # print(A)

    A.sort()

    cnt = 1
    if 1 in A:
        for i in A[A.index(1):]:
            if i == cnt:
                cnt+=1
    else:
        return 1

    return cnt
        

```

