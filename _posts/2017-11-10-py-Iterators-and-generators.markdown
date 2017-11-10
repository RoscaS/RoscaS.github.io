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
>>> print(x)
<list_iterator object at 0x000002284B8F6D68>
>>> print(x.__next__())
1
>>> print(x.__next__())
2
>>> print(x.__next__())
3
>>> print(x.__next__())
Traceback (most recent call last):
  File "\00.py", line 7, in <module>
    print(x.__next__())
StopIteration
```

À Chaque fois qu'on appel la méthode `__next__` sur itérateur, elle retourne l'élément suivant. Si il n'y a plus d'élément suivant, elle soulève une exception `StopIteration`.

Les itateurs sont implémentés sous forme de classe. Voici un itérateur qui fonctionne de la même façon qu la fonction built-in `range`:
