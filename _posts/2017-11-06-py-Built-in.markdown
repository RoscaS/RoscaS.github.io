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
La fonction `map` prend également une fonctoin **et** un itérable mais retourne un itérateur qui pplique la fonction (map la fonction) sur chacun des éléments dans l'itérable:

```py
a = lambda n: n*2

l = [1, 2, 3, 4, 5]

p = [i for i in map(a,l)]

print(', '.join([str(i) for i in p]))
```
**Output:**

```
2, 4, 6, 8, 10
```
