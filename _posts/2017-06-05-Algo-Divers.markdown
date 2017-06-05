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

## Digit extraction

```py
def digitExtract(n, pos):
	return (n//(10**pos))%10

for i in range(3,-1,-1):
    print(digitExtract(3684,i), end='|')

# output: 3|6|8|4|
```
