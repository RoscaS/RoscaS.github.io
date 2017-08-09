---
layout: post
title: "Py: Modules interessants"
subtitle: ""
date: 2017-08-01
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

## inspect et introspection

```
import inspect
```

Permet de retourner sous forme de string des infos d'introspection. Par exemple pour tracer les appels d'une fonction:

```python
import inspect

def cochon():
    print(inspect.stack()[0][3]) # cochon
    print(inspect.stack()[1][3]) # poule
    print(inspect.stack()[2][3]) # vache
    print(inspect.stack()[3][3]) # <module>

def poule():
    cochon()

def vache():
    poule()


vache()
```

**output**:

```
cochon
poule
vache
<module>
```

La première valeur entre `[]` définit le niveau d'introspection. 0 est la fonction actuelle, 1, celle qui apelle,... La seconde valeur entre `[]`, le type d'information.

* `[0][0]` $$ \Rightarrow $$ path du fichier
* `[x][1]` $$ \Rightarrow $$ adresse en mémoire du l'objet
* `[x][2]` $$ \Rightarrow $$ numéro de la ligne du call
* `[x][3]` $$ \Rightarrow $$ nom de la fonction
* `[x][4]` $$ \Rightarrow $$ string contenant le text de la ligne

** Le module comporte plein d'autres fonctions interessantes !**

Nous pouvons retourner le nom de la classe courante avec `self.__class__.__name__`.

Dans le cadre d'un héritage, cela retourne le nom de la classe qui hérite:

```python
import inspect

class Parent(object):
    def __init__(self, a=0):
        self.a = a
    
    def debug(self):
        print(inspect.stack()[1][3])
        print(self.__class__.__name__)


class Enfant(Parent):
    
    def poule(self):
        self.debug()


e1 = Enfant()
e1.poule()
```

**output**:

```
poule
Enfant
```


## Random

### randint

```python
random.randint(a, b)
```

Sort entier au hasard entre `a` et `b`

```python
>>> import random
>>> print(random.randint(1, 5))
4
```

Si nous avons uniquement besoin d'une plus valeur:

```python
>>> print(random.randint(1, 5)*100)
200
```

Plus large spectre:

```python
>>> for i in range(10):
...     print(random.randint(1,10000), end=', ')
4937, 248, 65, 5924, 7392, 2950, 4, 1577, 8702, 7708
```

### choice

Sort un élément d'une séquence:

```python
>>> print(random.choice("ABCDEFGHIJKLMNOPQRSTUVWXYZ"))
G
```

Equivalent du `popitem()` des dictionnaire:

```python
>>> l = ['a', 'b', 'c', 'd']
>>> r = random.choice(l)
>>> l.remove(b)
>>> print(l, b)
['a', 'b', 'd'] c
```

### shuffle


```python
>>> l = [i for i in range(10)]
>>> print(l)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> random.shuffle(l)
>>> print(l)
[4, 1, 7, 5, 6, 0, 8, 3, 9, 2]
```


### randrange

```python
randrange(start, stop=None, step=1, _int=int)
```

Sort un element au hasard dans `range(start, stop[, step])`
** A la différence de `randint()` nous pouvons ici définir une borne de départ uniquement.**