---
layout: post
title: "Prog: Divers"
subtitle: "Tricks et algorithmes qui aident"
date: 2017-04-02
author: Sol
category: Prog
tags: prog
finished: false
---


## How many digits in a number (py)

```py
from math import log10

def digitsNb(n):
    return log10(n)+1

tab = [1,12,123,1234,12345,123456]

for i in tab:
    print(int(digitsNb(i)), end=' ')

# output : 1 2 3 4 5 6
```

## Digit extraction (py)

```py
def digitExtract(n, pos):
	return (n//(10**pos))%10

for i in range(3,-1,-1):
    print(digitExtract(3684,i), end='|')

# output: 3|6|8|4|
```

## Nice round (Cpp)
Le cast en int (py ou cpp) va suprimer la décimale peu importe sa valeur.
Le `+ .5` avant le cast sur `tot` assure que la valeur après le cast sera arrondie en fonction de la décimale.

```cpp
int main() {
    double a = 10.25;
    int b = 17;
    int c = 5;

    float B = static_cast<float>(b);
    float C = static_cast<float>(c);
    float tot = a + (a*(B/100)) + (a*(C/100));

    cout << "The total meal cost is " << static_cast<int>(tot + .5) 
         << " dollars." << endl;

    return 0;
}
```
