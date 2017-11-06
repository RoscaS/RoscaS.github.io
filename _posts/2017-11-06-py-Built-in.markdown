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
La fonction `eval` est assez controversée dans la communauté Python. Elle prend une string en entrée et retourne son contenu executé...