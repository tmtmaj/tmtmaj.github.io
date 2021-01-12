---
title: "﻿[Algorithm] MaxCounters﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] MaxCounters](https://app.codility.com/programmers/lessons/4-counting_elements/max_counters/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(N, A):
    # write your code in Python 3.6
    # print(N, A)

    result = [0]*N
    max_count = 0
    
    # A가 단일 숫자로 이루어진 경우 (시간복잡도 해결을 위해)
    if len(set(A)) == 1:
        if A[0] != N+1:
            result[A[0] -1] = len(A)
            return result
        else:
            return result 

    
    for i in A:
        if i == N+1:
            result = [max_count]*N
            continue 
        result[i-1] += 1
        max_count = max(max_count, result[i-1])
        
    # 아래와 같이 구성시 performance에서 실패함, time limit 초과
    # for i in A:
        # if i == N+1:
            # result = [max(result)]*N
            # continue 
        # result[i-1] += 1

    return result
    
    # pass
```

