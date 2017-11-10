---
layout: post
title: "Py: Iterateurs et générateurs (bis)"
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

Les itateurs sont implémentés sous forme de classe. Voici un itérateur qui fonctionne de la même façon que la fonction built-in `range`:


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

La méthode `__iter__` **rend les objets issus de la classe iterables** et sa valeur de retour est un itérateur. 

> En background, la fonction `iter` appelle la méthode `__iter__` de l'objet passé en argument.

Pour bien faire, il faut définit une méthode `__next__` qui soulève l'exception `StopIteration` une fois qu'il n'y a plus d'éléments.

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

# Générateurs et mot clé Yield
Un générateur simplifie la création d'itérateurs. Un générateur est une fonction qui **produit une séquence de résultats à la place d'une valeur unique.**

```py
def yrange(n):
    i = 0
    while i < n:
        yield i
        i += 1
```

À chaque fois que le mot clé `yield` est executé, la fonction génère une nouvelle valeur:


```py
>>> y= yrange(3)
<generator object yrange at 0x000001A07846A3B8>
>>> y
0
>>> y.__next__()
1
>>> y.__next__()
2
>>> y.__next__()
Traceback (most recent call last):
    print(y.__next__())
StopIteration
```

Donc, un générateur est lui aussi un itérateur mais sa création est plus légère et on a pas à se soucier du protocole d'itération.

>Le mot "générateur" est a tord communément utilisé à la fois pour la fonction qui génère et ce qui est généré. Dans cet article, quand le mot "générateur" sera utilisé, il désignera l'objet généré. Pour désigner la fonction qui génère l'objet, on parlera de "fonction génératrice".

## Fonctionnement
<span style="color:red"> Le fonctionnement d'une **fonction génératrice** est sensiblement différent de celui d'une **fonction classique**!  </span> 

* Quand une fonction génératrice est appelée, un objet générateur est **directement retourné**, avant même de commencer à exécuter la première ligne du corp de la fonction.

* Une fois la méthode `__next__` appelée pour la première fois, la fonction s'exécute jusqu'à atteindre le mot clé `yield` (on peut le voir comme un "petit return") et la valeur "yieldée" est retournée.

Le snipet suivant démontre l'interaction entre `yield` et les appels de `__next__` sur l'objet générateur.


```py
>>> def foo():
...     print("begin")
...     for i in range(3):
...             print("before yield", i)
...             yield i
...             print("after yield", i)
...     print("end")
...
>>> f = foo()
>>> f.__next__()
begin
before yield 0
0
>>> f.__next__()
after yield 0
before yield 1
1
>>> f.__next__()
after yield 1
before yield 2
2
>>> f.__next__()
after yield 2
end
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

On voit clairement ici, qu'après le premier call de la méthode `__next__` la "tête de lecture" lit toutes les instructions entre le début de la fonction et le premier `yield` qui se trouve dans la boucle `for`, et renvoie renvoie la valeur de `i`. La "tête de lecture" reste sur la ligne du `yield` et une fois qu'on appelle une seconde fois la méthode `__next__`, la lectue du code reprend exactement où elle s'était arrété.

Voici un autre exemple:

```py
def integers():
    '''Infinite sequence of integers.'''
    i = 1
    while True:
        yield i
        i = i + 1

def squares():
    for i in integers():
        yield i * i

def take(n, seq):
    '''Returns first n values from the given sequence'''
    seq = iter(seq)
    result = []
    try:
        for i in range(n):
            result.append(seq.__next__())
    except StopIteration:
        pass
    return result

print(take(5, squares()))
```

# Generator Expression/comprehension

Les générateur en comprehension sont la version "générateur" de la liste comprehension. Elles ressemblent à ces dernières mais retournent un générateur à la place d'une liste.


```py
>>> a = (x*x for x in range(10))
>>> a
<generator object <genexpr> at 0x00000299D83CA3B8>
>>> sum(a)
285
```

Et elles sont bien pratiques comme arguments de diverses fonctions qui prennent des itérateurs:


```py
>>> sum((x*x for x in range(10)))
285
```

## Triplets de Pythagore

Disons que nous voulons trouver les 10 premiers (ou $n$ premiers) triplets de Pythagor. Un triplet `(x, y, z)` est dit de Pythagore si $x^2 + y^2 = z^2$.

Le problème est trivial à résoudre si nous savons jusque quelle valeur de $n$ tester. Mais nous, nous voulons trouver les $n$ premiers.

```py
def integers():
    '''Infinite sequence of integers.'''
    i = 1
    while True:
        yield i
        i = i + 1


def take(n, seq):
    '''Returns first n values from the given sequence'''
    seq = iter(seq)
    result = []
    try:
        for i in range(n):
            result.append(seq.__next__())
    except StopIteration:
        pass
    return result


pyt = ((x, y, z) for z in integers() for y in range(1,z) for x in range(1,y) if x*x+y*y==z*z)

print(take(10, pyt))
```

**Output**:

```
[(3, 4, 5), (6, 8, 10), (5, 12, 13), (9, 12, 15), (8, 15, 17), (12, 16, 20), (15, 20, 25), (7, 24, 25), (10, 24, 26), (20, 21,
 29)]
 ```

## Lecture de fichiers multiples

Nous voulons un programme qui prendra une liste de fichiers en argument et print le contenu de ces derniers. Un peu à la façon de `cat` sur Linux.

La façon traditionnelle d'implémenter ce programme serait:

```py
def cat(pattern, filenames):
    for f in filenames:
        for line in open(f):
            if pattern in line:
                print(line)
```

Maintenant, disons que nous ne voulons printer que les lignes qui contiennent une sous-string en particulier, comme la fonction `grep` sur Linux.


```py
def grep(pattern, filename):
    for f in filename:
        for line in open(f):
            if pattern in line:
                print(line)
```

Ces deux fonctions ont beaucoup en commun mais il est difficile de déplacer la partie commune dans une autre fonction. Les générateurs peuvent ici, nous aider:

```py
def readfiles(filename):
    for f in filename:
        for line in open(f):
            yield line

def grep(pattern, lines):
    return (line for line in lines if pattern in line)

def printlines(lines):
    for line in lines:
        print(line)

def main(pattern, filenames):
    lines = readfiles(filenames)
    lines = grep(pattern, lines)
    printlines(line)
```
 
 La complexité générale est bien moindre avec chaque fonction s'occupant d'une tache unique. Nous pouvons maintenant déplacer ces fonctions dans des modules séparés et les réutiliser dans d'autres programmes.