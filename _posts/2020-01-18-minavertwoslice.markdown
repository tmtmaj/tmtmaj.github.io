---
title: "﻿[Algorithm] MinAvgTwoSlice﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] [MinAvgTwoSlice]](https://app.codility.com/programmers/lessons/5-prefix_sums/min_avg_two_slice/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(A):
    # # write your code in Python 3.6
    # accum_A = [0]
    # for i in range(len(A)):
    #     accum_A.append(accum_A[i]+A[i])

    # # print(accum_A)
    
    # min_num = (A[0]+A[1]) /2
    # answer = 0
    # for i in range(len(A)-1):
    #     for j in range(i+2,len(A)+1):
    #         print(accum_A[j], accum_A[i],(j -i),  (accum_A[j] - accum_A[i]) / (j -i))
    #         if min_num > (accum_A[j] - accum_A[i]) / (j -i):
    #             min_num = (accum_A[j] - accum_A[i]) / (j -i)
    #             answer = i

    min_num  = (A[0]+A[1]) /2
    answer = 0

    for i in range(2, len(A)):
        
        if min_num > (A[i-2] + A[i-1] + A[i])/3:
            min_num = (A[i-2] + A[i-1] + A[i])/3
            answer = i-2

        if min_num > (A[i-1] + A[i])/2:
            min_num = (A[i-1] + A[i])/2
            answer = i-1

    

    return answer
    pass
```

