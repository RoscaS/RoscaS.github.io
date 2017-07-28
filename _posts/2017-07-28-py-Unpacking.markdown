---
layout: post
title: "Py: Unpacking"
subtitle: "Unpacking et opérateur splat (*)"
date: 2017-07-28
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

#### sources

* [sam&max](http://sametmax.com/comment-utiliser-yield-et-les-generateurs-en-python/)

# Unpacking

Permet de prendre chaque élément d'un itérable et de les attribuer à des variables distinctes en une fois.

```python
>>> drapeau           = ("noir", "jaune", "rouge")
>>> premiere_couleur  = drapeau[0]
>>> deuxieme_couleur  = drapeau[1]
>>> troisieme_couleur = drapeau[2]
>>> 
>>> print(premiere_couleur)
noir
>>> print(deuxieme_couleur)
jaune
>>> print(troisieme_couleur)
rouge
```

Et maintenant la même opération en une ligne:

```python
>>> couleur1, couleur2, couleur3 = drapeau
>>> print(couleur1)
noir
>>> print(couleur2)
jaune
>>> print(couleur3)
rouge
```

Il n'y a rien de particulier à faire. **L'Unpacking est automatique**. Il suffit de mettre à guche du signe`=` le même nombre de variables qu'il y a d'éléments dans la séquence à droite du signe `=`. Si ce n'est pas le cas, Python retournera une erreur explicite.

```python
>>> un, deux = drapeau
ValueError: too many values to unpack (expected 2)
```

```python
>>> un, deux, trois, quatre = drapeau
ValueError: not enough values to unpack (expected 4, got 3)
```

## Opérateur `*`

Il nous permet de gérer le cas où il y a plus d'éléments que de variables en disant "je veux que cette variable contienne le reste":

```python
>>> couleur1 , *autres_couleurs = drapeau
>>> print(couleur1)
noir
>>> print(autres_couleurs)
['jaune', 'rouge']
```

Et en plus c'est intelligent:

```python
>>> *autres_couleurs, derniere_couleur = drapeau
>>> print(autres_couleurs)
['noir', 'jaune']
>>> print(derniere_couleur)
rouge
```

Il permet également de forcer l'Unpacking dans le cas où c'est ambigu. La fonction qui suit ne fait qu'afficher chacun de ses paramètres:




