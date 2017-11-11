---
layout: post
title: "Py: Special tactics"
subtitle: "Pyhton Ninja"
date: 2017-11-10
author: Sol
category: Py
tags: 
finished: true
mathjax: true
---
# Conteneurs

## Compter des occurences

```python
>>> import random
>>> from collections import Counter
>>>
>>> l = [random.randint(1,10) for i in range(1000)]
>>>> print(Counter(l))
Counter({5: 115, 8: 111, 7: 104, 2: 102, 1: 101, 3: 100, 6: 97, 4: 94, 10: 90, 9: 86})
```

```python
>>> s = "j'aime la viande rouge bien cuite"
>>> print(Counter(s))
Counter({'e': 5, ' ': 5, 'i': 4, 'a': 3, 'u': 2, 'n': 2, 'j': 1, 'r': 1, 'b': 1, 'd': 1,
'm': 1, 't': 1, "'": 1, 'l': 1, 'v': 1, 'c': 1, 'g': 1, 'o': 1})
```

### Sous forme de tuples
Permet de pouvoir utiliser un indexe pour sortir directement une valeur:

```python
s = "j'aime la viande rouge bien cuite"
counted = Counter(s).most_common()
print("Le char '{}' apparait le plus dans la string:\n{}\n\
    Nombre d'occurences: {}\n Suivi par le char '{}' \
    qui apparait {} fois.".format(
        counted[0][0], s, counted[0][1], counted[1][0], counted[1][1]))
```

**return:**

```
Le char e apparait le plus dans la string:
j'aime la viande rouge bien cuite
    Nombre d'occurences:5
 Suivi par le char ' '     qui apparait 5 fois.
```


## List reverse

```py
>>> x = range(10)
>>> x[::-1]
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

## Multi list iteration
```py
>>> names = ['a', 'b', 'c']
>>> values = [1, 2, 3]
>>> for name, values, in zip(names, values):
...     print(name, values)
...
a 1
b 2
c 3
```

## Méthode sort et sorted built-in

La méthode `sort` trie **une liste**. Le tri est stable (voir algo: tri).

```py
>>> a = [2 ,10, 4, 3, 7]
>>> a.sort()
>>> a
[2, 3, 4, 7, 10]
```

Il est possible de spécifier une fonction comme argument de tri.
Ici nous trions en fonction du second élément des sous-listes:

```py
>>> a = [[2, 3], [4, 6], [6, 1]]
>>> a.sort(key=lambda x: x[1])
>>> a
[[6, 1], [2, 3], [4, 6]]
```

> Noter qu'un print de `a.sort()` retournera `none`.

Et là, en fonction de la taille des éléments:

```py
>>> l = ['python', 'perl', 'java', 'c', 'haskell', 'ruby']
>>> l.sort(key=lambda x: len(x))
>>> l
['c', 'perl', 'java', 'ruby', 'python', 'haskell']
```

La fonction `sorted` prend **une séquence** et retourne une liste des éléments triée (ne modifie pas la source).

```py
>>> a = [2 ,10, 4, 3, 7] # list
>>> sorted(a)
[2, 3, 4, 7, 10]
>>> a
[2 ,10, 4, 3, 7]
>>>
>>> b = (2 ,10, 4, 3, 7) # tuple
>>> sorted(b)
[2, 3, 4, 7, 10]
>>> tuple(sorted(b))
(2, 3, 4, 7, 10)
>>> b
[2, 3, 4, 7, 10]
```


