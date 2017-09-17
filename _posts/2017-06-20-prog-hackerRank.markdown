---
layout: post
title: "Prog: HackerRank"
subtitle: "practice HackerRank"
date: 2017-06-19
author: Sol
category: Prog
tags: prog hackerRank
finished: false
mathjax: true
---

# C++

## Variable Sized Array
[Original](https://www.hackerrank.com/challenges/variable-sized-arrays)

Concidérons un tableau $ a$ de $ n$ éléments où chaque indexe $ i$ contient une référence à un autre tableau de $ k_i$ entiers (dans lequel la valeur de $ k_i$ varie d'un tableau à l'autre). 

![](https://s3.amazonaws.com/hr-challenge-images/14507/1476906485-2c93045320-variable-length-arrays.png)

Pour un $ a$ donné, nous devons répondre à $ q$ requêtes. Les requêtes sont faites dans le format `i` `j`, où $ i$ désigne un indice dans le tableau $ a$ et $ j$ désigne un indice dans le tableau situé dans $ a[i]$. Pour chaque requête, **trouver** et **printer** la valeur de l'élément $ j$ dans le tableau situé à l'indice $ a[i]$ sur une nouvelle ligne.

> Sur l'énnoncé du problème original (en) ils nous conseillent d'utiliser un [vecteur](http://www.cplusplus.com/reference/vector/vector/) et non pas un tableau basique.

### Format de l'input

La première ligne contient deux entiers séparés par un espace désignant respectivement la valeur de $ n $ (le nombre de tableaux de taille variable) et $ q $ (le nombre de requêtes).
Chaque ligne $ i $ de la série de lignes $ n $ suivantes contient une séquence d'éléments au format $ k, \; a[i]_0, \; a[i]_1, \; \ldots, \; a[i]_{k-1} $ séparés par un espace et décrivant le $ k $ ième tableau se trouvant à l'indice $ a[i] $. 
Chaqu'une des $ q $ lignes suivantes contient des entiers séparés par un espace décrivant respectivement les valeurs de $ i $ (un indice du tableau $ a $) et $ j $ (un indice dans le tableau référencé par $ a[i] $) pour une requête.

### Contraintes

* $ 1 \leq n \leq 10^5 $
* $ 1 \leq q\leq 10^5 $
* $ 1 \leq \forall k \leq 3 \cdot 10^5 $
* $ 1 \leq \Sigma k \leq 3 \cdot 10^5 $
* $ 0 \leq \forall i \ < n $
* $ 0 \leq \forall j \ < k $

<br>

* Tous les indices commencent à  $0$
* Les valeurs entrées en input appartiennent à l'ensemble **N** et ne sont pas plus grandes que $ 10^6 $

### Exemple d'input

```
2 2
3 1 5 4
5 1 2 8 9 3
0 1
1 3
```

### Exemple d'output

```
5
9
```

### Explication
Ce diagramme représente notre exemple d'input:

![](https://s3.amazonaws.com/hr-challenge-images/14507/1476906485-2c93045320-variable-length-arrays.png)

Nous effectuons les $ q = 2 $ requêtes suivantes:

1. Trouver le tableau situé à l'indice  $ i = 0 $ qui correspond à $ a[0] = [1,5,4]$ . Nous devons `print` les valeurs à l'indexe $ j = 1 $ de ce tableau qui comme nous pouvons le voir est $ 5 $.

2. Trouver le tableau à l'indexe $ i = 1 $, qui correspond à $ a[1] = [1,2,8,9,3] $. Nous devons `print` la valeur à l'indexe $ j = 3 $ de ce tableau qui comme nous pouvons le voir est 9.


### Solution

```c++
#include <cstdio>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    int tabs, q;
    int size, val;
    int x,y;

    cin >> tabs >> q;

    vector<vector<int>> tabsVec;

    for (int i = 0; i < tabs; ++i) {
        vector<int> temp;
        cin >> size;
        for (int j = 0; j < size; ++j) {
            cin >> val;
            temp.push_back(val);
        }
        tabsVec.push_back(temp);
    }

    for (int i = 0; i < q; ++i) {
        cin >> x >> y;
        cout << tabsVec[x][y] << "\n";
    }

    return 0;
}
```

### Liens

[C++ competitive programming I/O](https://marcoarena.wordpress.com/2016/03/13/cpp-competitive-programming-io/)