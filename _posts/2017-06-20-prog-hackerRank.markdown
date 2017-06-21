---
layout: post
title: "Prog: HackerRank"
subtitle: "practice HackerRank"
date: 2017-06-19
author: Sol
category: Prog
tags: prog hackerRank
finished: false
---

# C++

## Variable Sized Array
[Original](https://www.hackerrank.com/challenges/variable-sized-arrays)

Concidérons un tableau $$ \large a$$ de $$ \large n$$ éléments où chaque indexe $$ \large i$$ contient une référence à un autre tableau de $$ \large k_i$$ entiers (dans lequel la valeur de $$ \large k_i$$ varie d'un tableau à l'autre). 

![](https://s3.amazonaws.com/hr-challenge-images/14507/1476906485-2c93045320-variable-length-arrays.png)

Pour un $$ \large a$$ donné, nous devons répondre à $$ \large q$$ requêtes. Les requêtes sont faites dans le format `i` `j`, où $$ \large i$$ désigne un indice dans le tableau $$ \large a$$ et $$ \large j$$ désigne un indice dans le tableau situé dans $$ \large a[i]$$. Pour chaque requête, **trouver** et **printer** la valeur de l'élément $$ \large j$$ dans le tableau situé à l'indice $$ \large a[i]$$ sur une nouvelle ligne.

> Sur l'énnoncé du problème original (en) ils nous conseillent d'utiliser un [vecteur](http://www.cplusplus.com/reference/vector/vector/) et non pas un tableau basique.

### Format de l'input

La première ligne contient deux entiers séparés par un espace désignant respectivement la valeur de $$ \large n $$ (le nombre de tableaux de taille variable) et $$ \large q $$ (le nombre de requêtes).
Chaque ligne $$ \large i $$ de la série de lignes $$ \large n $$ suivantes contient une séquence d'éléments au format $$ \large k, \; a[i]_0, \; a[i]_1, \; \ldots, \; a[i]_{k-1} $$ séparés par un espace et décrivant le $$ \large k $$ ième tableau se trouvant à l'indice $$ \large a[i] $$. 
Chaqu'une des $$ \large q $$ lignes suivantes contient des entiers séparés par un espace décrivant respectivement les valeurs de $$ \large i $$ (un indice du tableau $$ \large a $$) et $$ \large j $$ (un indice dans le tableau référencé par $$ \large a[i] $$) pour une requête.

### Contraintes

$$ \large 1 \leq n \leq 10^5 $$

$$ \large 1 \leq q\leq 10^5 $$

$$ \large 1 \leq \forall k \leq 3 \cdot 10^5 $$

$$ \large 1 \leq \Sigma k \leq 3 \cdot 10^5 $$

$$ \large 0 \leq \forall i \ < n $$

$$ \large 0 \leq \forall j \ < k $$

* Tous les indices commencent à  0
* Les valeurs entrées en input appartiennent à l'ensemble **N** et ne sont pas plus grandes que $$ \large 10^6 $$

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

Nous effectuons les $$ \large q = 2 $$ requêtes suivantes:

1. Trouver le tableau situé à l'indice  $$ \large i = 0 $$ qui correspond à $$ \large a[0] = [1,5,4]$$ . Nous devons `print` les valeurs à l'indexe $$ \large j = 1 $$ de ce tableau qui comme nous pouvons le voir est $$ 5 $$.

2. Trouver le tableau à l'indexe $$ \large i = 1 $$, qui correspond à $$ \large a[1] = [1,2,8,9,3] $$. Nous devons `print` la valeur à l'indexe $$ \large j = 3 $$ de ce tableau qui comme nous pouvons le voir est 9.


### Resolution

```c++
#include <cmath>
#include <string>
#include <cstdio>
#include <vector>
#include <iostream>
using namespace std;


void display(vector<vector<int>> t) {
    for (int i = 0; i <= t.size(); i++) {
        for (int j = 0; j <= t[i].size(); i++) {
            cout << t[i][j] << " ";
        }
        cout << endl;
    }
}


int main() {
    int n; // nbr of tabs in ptr tab
    int k; // nbr of values in tabs
    int q; // nbr of queries
    int x; // value of [i][j]s

    // cin >> n, q;
    // cin >> n;

    vector<vector<int>> vecTab;

    // for (int i = 1; i <= n; ++i) {
    //     int input;
    //     while (cin >> input) {
    //         vecTab[i].push_back(input);
    //     }
    // }

    vector<vector<int>> a;

    while (cin >> n) {
        vector<int> line(n);
        for (int col = 0; col < n; ++col) {
            cin >> line[col];
        }

        a.push_back(line);
    }


    display(vecTab);

    return 0;
}
```

[C++ competitive programming I/O](https://marcoarena.wordpress.com/2016/03/13/cpp-competitive-programming-io/)