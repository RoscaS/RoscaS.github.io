---
layout: post
title: "Py: built-ins"
subtitle: "Built-ins moins connues de Python"
date: 2017-11-05
author: Sol
category: Py
tags: tools
finished: true
mathjax: true
---

## Source

* [Python 201: intermediat Python](https://www.blog.pythonlibrary.org/2017/03/08/python-201-is-now-an-online-course/)

# Any
La fonction `any` prend un **itérable en entrée** et retourne un **booléen** si au moins un élément de l'itérable est `True`:

```py
>>> any([0,0,0,1])
True
```

> You might be wondering when you would ever use this built-in. I did too at first. An example that croppedupinoneofmyjobsinvolvedaverycomplexuseriterfacewhereIhadtotestvariouspieces offunctionality.OneoftheitemsthatIneededtotestwasifacertainlistofwidgetshadbeenshown or enabled when they shouldn’t be. The any built-in was very useful for that. Here’s an example that kind of demonstrates what I’m talking about, although it’s not the actual code I used:

```py
>>> widget_one = '' 
>>> widget_two = '' 
>>> widget_three = 'button' 
>>> widgets_exist = any([widget_one, widget_two, widget_three]) 
>>> widgets_exist 6 
True
```

> Basically I would query the user iterface and ask it if widgets one through three existed and put the responses into a list. If any of them returned True, then I’d raise an error. You might want to check out Python’s all built-in as it has similar functionality except that it will only return True if every single item in the iterable is True.

# Eval
La fonction `eval` est assez controversée dans la communauté Python. Elle prend une string en entrée et execute son contenu.

```py
>>> var = 10
>>> source = 'var * 2'
>>> eval(source)
20
```
>Evaluate the given source in the context of globals and locals. The source may be a string representing a Python expression or a code object as returned by compile(). The globals must be a dictionary and locals can be any mapping, defaulting to the current globals and locals. If only globals is given, locals defaults to it

# filter

La fonction `filter` prend un une fonction **et** un itérable en argument et retourne un itérateur sur les éléments de l'itérable qui retournent `True`:

```py
import random

a = lambda i: i < 10

l = [random.randint(1,20) for i in range(20)]

p = [i for i in filter(a, l)]

print('l: {}\np: {}'.format(l, p))
```

**Output:**

```
l: [5, 3, 13, 13, 10, 15, 13, 8, 6, 1, 20, 7, 13, 3, 1, 10, 7, 20, 4, 2]
p: [5, 3, 8, 6, 1, 7, 3, 1, 7, 4, 2]
```

# map
La fonction `map` prend également une fonction **et** un itérable mais retourne un itérateur qui applique la fonction (map la fonction) sur chacun des éléments dans l'itérable:

```py
f = lambda x: x*2
l = [1, 2, 3, 4, 5]

print(list(map(f,l)))
```
**Output:**

```
[2, 4, 6, 8, 10]
```

> `type(map(f,l))` retourne un objet de type `map`; c'est notre itérateur.

Les fonction filter et map dupliquent les fonctionnalités des générateurs d'expression en Python 3 et celle des list comprehension en Python 2.

Les façons de faire suivantes sont tout à fait équivalentes au précédent et le choix de la méthode est une question de style.

```py
f = lambda x: x*2
l = [1, 2, 3, 4, 5]

print([i for i in map(f, l)])
print([f(i) for i in l])
print([i*2 for i in l])
```

# Zip
La fonction `zip` prend en argument une série d'itérables et crée un agrégat des éléments de chaque itérable.

```py
k = ['x', 'y', 'z']
v = [5, 6, 7]

print(zip(k,v))
print(list(zip(k,v)))
print(dict(zip(k,v)))
```

**Output:**

```
<zip object at 0x000001B02EC57A08>
[('x', 5), ('y', 6), ('z', 7)]
{'z': 7, 'x': 5, 'y': 6}
```

Si les deux listes sont de taille différente, le zip se fait quand même mais les éléments supplémentaires dans la liste la plus longue ne seront pas traités.
L'usage le plus populaire de la fonction `zip` est sa capacité de créer un dictionnaire entre deux listes.

# dir

Cette fonction appelée sans paramètres retourne les noms de l'espace nom courant:

```py
a = 3
b = 'string'
print(dir())
```

**Output:**

```
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'a', 'b']
```

Au sein d'une fonction:

```py
a = 3
b = 'string'

def fonction():
    x = 1
    y = 2
    print(dir())

fonction()
```

**Output:**

```
['x', 'y']
```

