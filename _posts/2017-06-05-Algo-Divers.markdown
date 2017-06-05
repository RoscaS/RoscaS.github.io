---
layout: post
title: "Algo: Divers"
subtitle: "Algorithmes divers"
date: 2017-06-05
author: Sol
category: Algo
tags: algo
finished: false
---


## How many digits in a number

```py
from math import log10

def digitsNb(n):
    return log10(n)+1

tab = [1,12,123,1234,12345,123456]

for i in tab:
    print(int(digitsNb(i)), end=' ')

# output : 1 2 3 4 5 6
```

## Digit extraction

```py
def digitExtract(n, pos):
	return (n//(10**pos))%10

for i in range(3,-1,-1):
    print(digitExtract(3684,i), end='|')

# output: 3|6|8|4|
```
