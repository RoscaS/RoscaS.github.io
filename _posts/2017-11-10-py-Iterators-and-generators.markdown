---
layout: post
title: "Py: Iterateurs et générateurs"
subtitle: "Iterateurs et générateurs"
date: 2017-11-10
author: Sol
category: Py
tags: 
finished: true
mathjax: true
---

# Itérateurs

On utilise des boucles `for` pour itérer sur une liste:
```py
>>> for i in [1, 2, 3, 4]:
...    print(i)
...
1
2
3
4
```

Si on l'utilise sur une string, on itère sur les char:

```py
>>> for c in "poule":
...    print(c)
...
p
o
u
l
e
```

Si on l'utilise sur un dictionnaire, on itère sur les clés:

```py
>>> for k in {"x": 1, "y": 2}:
...    print(k)
...
y
x
```

**Si on l'utilise sur un fichier, on itère sur les lignes**:

```py
>>> for line in open("a.txt"):
...    print(line)
...
ligne 1
ligne 2
```

Il y a donc de nombreux types d'objets qui peuvent être utilisés avec une boucle for. Ces objets sont dit _objets **itérables**_ et il y a de nombreuses fonctions qui utilisent ces itérables.

# Protocole d'itération
La fonction built-in `iter` prend un itérable et retourne un itérateur:


```py
>>> x = iter([1, 2, 3])
>>> x
<list_iterator object at 0x000002284B8F6D68>
>>> x.__next__()
1
>>> x.__next__()
2
>>> x.__next__()
3
>>> x.__next__()
Traceback (most recent call last):  
    x.__next__()
StopIteration
```

À Chaque fois qu'on appel la méthode `__next__` sur itérateur, elle retourne l'élément suivant. Si il n'y a plus d'élément suivant, elle soulève une exception `StopIteration`.

Les itateurs sont implémentés sous forme de classe. Voici un itérateur qui fonctionne de la même façon qu la fonction built-in `range`:


```py
class Yrange:
    def __init__(self, n):
        self.i = 0
        self.n = n

    def __iter__(self):
        return self

    def __next__(self):
        if self.i < self.n:
            i = self.i
            self.i += 1
            return i
        else:
            raise StopIteration()
```

La méthode `__iter__` **rend les objets issus de la classe iterables** et sa valeur de retour est un itérateur. Pour bien faire, il faut définit une méthode `__next__` qui soulève l'exception `StopIteration` une fois qu'il n'y a plus d'éléments.

```py
>>> y. Yrange(3)
>>> y.__next__()
0
>>> y.__next__()
1
>>> y.__next__()
2
>>> y.__next__()
Traceback (most recent call last):
  File "00.py", line 27, in <module>
    print(y.__next__())
Iterators&generators\01Range-imlpem.py", line 20, in __next__
    raise StopIteration()
StopIteration
```

De nombreuses built-in aceptent les itérateurs comme arguments:

```py
>>> list(Yrange(5))
[0, 1, 2, 3, 4, 5]
>>> sum(Yrange(5))
10
```

