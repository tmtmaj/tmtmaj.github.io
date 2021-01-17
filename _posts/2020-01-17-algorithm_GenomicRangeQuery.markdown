---
title: "﻿[Algorithm] GenomicRangeQuery﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] [GenomicRangeQuery]](https://app.codility.com/programmers/lessons/5-prefix_sums/genomic_range_query/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(S, P, Q):
    # write your code in Python 3.6
    # print(S, P, Q)

    
    cost_dict = {'A':1, 'C':2, 'G':3, 'T':4}

    curr_counts = [0,0,0,0]
    counts = [curr_counts[:]]
    for s in S:
        curr_counts[cost_dict[s]-1] += 1
        counts.append(curr_counts[:])

    # print(counts)

    results = []
    for i in range(len(Q)):
        counts_q = counts[Q[i] + 1]
        counts_p = counts[P[i]]

        if Q[i] == P[i]:
            results.append(cost_dict[S[Q[i]]])
        elif counts_q[0] > counts_p[0]:
            results.append(1)
        elif counts_q[1] > counts_p[1]:
            results.append(2)
        elif counts_q[2] > counts_p[2]:
            results.append(3)
        elif counts_q[3] > counts_p[3]:
            results.append(4)

    return results


    # # OK 하지만 이해가 잘안됌 어디가 되는거지..
    # result = []
    # for i in range(len(P)):
    #     if 'A' in S[P[i]:Q[i]+1]:
    #         result.append(1)
    #     elif 'C' in S[P[i]:Q[i]+1]:
    #         result.append(2)
    #     elif 'G' in S[P[i]:Q[i]+1]:
    #         result.append(3)
    #     else:
    #         result.append(4)
    # return result

    # fac_dict = {'A':1, 'C':2, 'G':3, 'T':4}
    # S_int = [fac_dict[i] for i in S]
    # result = []

    # # time error
    # for p, q in zip(P, Q):
    #     if 1 in S_int[p:q+1]:
    #         result.append(1)
    #     elif 2 in S_int[p:q+1]:
    #         result.append(2)
    #     elif 3 in S_int[p:q+1]:
    #         result.append(3)
    #     else:
    #         result.append(4)

    # return result 

    # # time error
    # for p, q in zip(P, Q):
    #     result.append(min(S_int[p:q+1]))    
    # return result    

       
    pass
```

