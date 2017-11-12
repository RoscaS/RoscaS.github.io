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

Pareil avec des strings.

## Reversed
Cette fonction renvoie un itérateur qui itère à l'envers sur une séquence:

```py
>>> for i in reversed([1,2,3]):
...     print(i)
...
3
2
1
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

## Strip
Cette méthode retourne une copie de la string passée en argument et avec les éventuels espace avant et après supprimés. On peut égallement lui passer une string en argument ce qui fait qu'à la place de retirer les éventuels espaces, la méthode retirera les éventuels cha de la string passée en argument:

```py
>>> print(':abcd.efg!'.strip('.?!;:'))
abcd.efg
```

# files

`text.txt`:
```
Integer tortor leo, faucibus id turpis non, gravida egestas
tortor. Curabitur blandit eget ipsum in malesuada. Aenean
elit elit, aliquam convallis tellus sit amet, elementum
semper nisl. 
```

```py
def linecount(file):
    return len(open(file).readlines())

def wordcount(file):
    return len(open(file).read().split())

def charcount(file):
    return len(open(file).read())


F = 'text.txt'

print(charcount(F))
print(wordcount(F))
print(linecount(F))
```

**output:**
```
187
27
4
```

La méthode `read()` retourne une string avec le nombre char spécifié en argument (tout le fichier si aucun argument n'est spécifié).

La méthode `readlines()` retourne une liste de string des lignes du fichier.

la méthode `readline()` retourne les lignes du fichier dans une string une à la fois. Si il n'y a plus rien à lire, la méthode retourne des string vides.


```py
>>> f = open('text.txt', 'w')
>>> f.writelines(['a\n', 'b\n', 'c\n'])
>>> f.close()
>>> print(open('text.txt').read())
a
b
c

```

La méthode `writelines` est pratique quand les données se trouvent dans une liste composée de strings.

Exemple:

`text.txt`
```
Integer tortor leo, faucibus id turpis non, gravida egestas
tortor. Curabitur blandit eget ipsum in malesuada. Aenean
elit elit, aliquam convallis tellus sit amet, elementum
semper nisl. 
```

```py
def reverse_text(file):
    for i in reversed(file):
        print(i)

F = 'text.txt'

f = open(F)
l = []
for c, i in enumerate(f):
    l.append('{}) {}'.format(c+1, i))

f.close()

f = open(F, 'w')
f.writelines(l)

f.close()

reverse_text(F)
```

**output:**

```
4) semper nisl.
3) elit elit, aliquam convallis tellus sit amet, elementum
2) tortor. Curabitur blandit eget ipsum in malesuada. Aenean
1) Integer tortor leo, faucibus id turpis non, gravida egestas
```

Dans cet exemple, la fonction `reversed()` renvoie un itérateur qui itère à l'envers sur la liste.

# List comprehension

Triplets de Pythagore:

```py
>>> print([(x, y, z) for x in range(1, n) for y in range(x, n) for z in range(y, n) if x*x + y*y == z*z])
[(3, 4, 5), (5, 12, 13), (6, 8, 10), (8, 15, 17), (9, 12, 15), (12, 16, 20)]
```
