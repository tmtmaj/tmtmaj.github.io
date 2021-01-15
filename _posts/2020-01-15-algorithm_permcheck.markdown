---
title: "﻿[Algorithm] PermCheck﻿"
layout: post
author: Jeonghyeok Park
categories: Algorithm
tags: Algorithm
---

﻿

[[Codility] [PermCheck]](https://app.codility.com/programmers/lessons/4-counting_elements/perm_check/)



[Code]

```
# you can write to stdout for debugging purposes, e.g.
# print("this is a debug message")

def solution(A):
    # write your code in Python 3.6

    import collections
    def check_duplic(a: list):
        # print(collections.Counter(a).values(), len(collections.Counter(a).values()))
        return False if len(set(collections.Counter(a).values())) == 1 else True
    # print(A)
    if len(A) == max(A) and not check_duplic(A):
        return 1
    else:
        return 0
    pass
```

