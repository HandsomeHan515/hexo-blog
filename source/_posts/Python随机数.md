---
title: Python随机数
date: 2017-09-20 16:12:22
tags: Algorithm
categories: Python
---
# Python随机数
    
```
# N 代表随机数的位数
import random, string

code = ''.join(random.choice(string.ascii_uppercase + string.digits) for _ in range(N))
```