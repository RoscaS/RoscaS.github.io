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

```python
def afficher(elem1, elem2=None, elem3=None):
    print(elem1)
    print(elem2)
    print(elem3)
```

```python
>>> drapeau = ("noir", "jaune", "rouge")
>>> afficher(drapeau)
('noir', 'jaune', 'rouge')
None
None
```

Sans surprises pour ce premier snippet. Le prochain, nous montre l'utilisation de `*` pour forcer l'unpacking de façon à ce que les valeurs du tuple **soient passées individuellement comme autant de paramètres**.

```python
>>> afficher(*drapeau)
noir
jaune
rouge
```

Ce qui s'utilise dans le cas où nous utilisons une collection tout au long d'un programme pour ne pas trainer des variables intermédiaires.

Cette façcon de faire fonctionne aussi pour les slices:

```python
>>> l = [1, 2, 3, "element qu'on ne veut pas"]
>>> afficher(*l[:-1])
1
2
3
```

## Opérateur `**`

Cet opérateur est utilisé pour forcer l'Unpacking des **dictionnaires**. Les valeurs du dictionnaire deviennent les valeurs des paramètres, mais cette association se fait par nom: **Chaque clé du dictionnaire doit correspondre à un nom de paramètre**:

```python
>>> elements = {"elem1": "eau", "elem2": "feu", "elem3": "air"}
>>> afficher(**elements)
eau
feu
air
```

Si une clé ne possède pas le nom adéquat, Python retournera une erreur:

```python
>>> elements = {"elem1": "eau", "elem2": "feu", "poule": "air"}
>>> afficher(**elements)
TypeError: afficher() got an unexpected keyword argument 'poule'
```

Une autre erreur est d'utiliser `*` avec un dictionnaire qui nous retournera les clés:

> Les dictionnaires ne sont pas ordonnés, l'ordre des clés est aléatoire

```python
>>> elements = {"elem1": "eau", "elem2": "feu", "elem3": "air"}
>>> afficher(*elements)
elem2
elem3
elem1
>>> afficher(*elements)
elem3
elem1
elem2
```

Si on donne moins de valeurs qu'il n'y a de paramètres, Python remplit tout ce qu'il peut:

```python
>>> drapeau = ("noir", "jaune", "rouge")
>>> afficher(*drapeau[:-1])
noir
jaune
None
```

Dans le cas inverse, si il y a plus d'éléments que de paramètres, Python renvoie une erreur:

```python
>>> drapeau = ("noir", "jaune", "rouge", "gris")
>>> afficher(*drapeau)
TypeError: afficher() takes from 1 to 3 positional arguments but 4 were given
```

# Paramétrage dynamique

<span style="color:red"> Attention, cet usage est souvent confondu avec le précédent. </span> 

Dans certain cas, la définition d'un fonction qui accepte un nombre infini de paramètres est utile. L'exemple innutile suivant en est un exemple:

```python
def mul(a, b):
    return a * b

>>> print(mul(2, 3))
6
```

Si on veut rajouter un 3e paramètre, il faut redéfinir la fonction, pareil pour un 4e... Finalement on finit par demander de passer une liste pour permettre un nombre arbitraire de paramètres:

```python
def mul(tab):
    res = 1
    for i in tab:
        res = res *i
    return res

>>> print(mul([2, 3, 4, 5]))
120
```

`*` autorise le passage d'une infinité de paramètres ce qui nous permet de ne pas utiliser de liste pour stocker les arguments:

```python
def mul(*elems):
    res = 1
    for i in elems:
        res = res * i
    return res

>>> print(mul(2, 3, 4, 5))
120
```

Tous les arguments sont automatiquement stockés dans une liste quiest le paramètre qu'on désigne par `*`.

Cette façon de faire peut être utilisée conjointement avec des paramètres normaux:

```python
def display(elem1, elem2, *elemX):
    print(elem1)
    print(elem2)
    for e in elemX:
        print("(*) {}".format(e))

>>> display("poule", "cochon", "vache", "poney", "mouton")
poule
cochon
(*) vache
(*) poney
(*) mouton
```
Le paramètre flanqué de `*` est **toujours le dernier** et il ne doit y en avoir qu'un. 

<span style="color:red"> **Par convention cet argument se nome** </span> `*args`.

Nous pouvons utiliser de la même façon `**` pour les dictionnaires:

```python
def display_recette(recette, **ingredients):
    print(recette)
    for ingredient in ingredients.items():
        print(" - %s: %s" % ingredient)

display_recette(
    "moules frites",
    creme="une chie",
    moules="deux trois",
    frites="une chie aussi"
)
```

**output**:

```
moules frites
 - frites: une chie aussi
 - moules: deux trois
 - creme: une chie
```

De la même façon il faut mettre `**` en dernier. 
<span style="color:red"> **Par convention cet argument se nome** </span> `**kwargs` (keyword arguments).

Il est également possible de tout mélanger:

```python
def display_hybride(param_normal,
                    param_defau="default value",
                    *argsm,
                    **kwargs):
    print(param_normal)
    print(param_defau)
    print(argsm)
    print(kwargs)


display_hybride(
    "param1", "param2", "infini1",
    "infini2", kwinfini1=1, kwinfini2=2
)
```

**output:**

```
param1
param2
('infini1', 'infini2')
{'kwinfini2': 2, 'kwinfini1': 1}
```

<span style="color:red"> Nous devons absolument mettre les paramètresdans l'ordre suivant: </span> 

1. paramètres normaux et obligatoires;
2. paramètres normaux facultatifs (default;)
3. paramètres dynamiques;
4. paramètres dynamiques nommés.

Cela permet de faire jouer les valeurs par défaut de façon très souple:

```python
>>> display_hybride("rien qu'un param")
rien qu'un param
default value
()
{}
```

On peut définir des paramètres qui ne peuvent être passés qu'en spécifiant leur nom ("keyword only parameters"). Pour ce faire, il faut mettre au moins un `*`, et tout ce qui suit et qui n'est pas flanqué de `*` ne peut plus être passé comme argument positionnel:

```python
def poule(normal, *args, keyword_only):
    print(normal, args, keyword_only)

>>> poule("yeah", "cool", "wesh")
TypeError: poule() missing 1 required keyword-only argument: 'keyword_only'
>>>
>>> poule("yeah", "cool", keyword_only="man")
yeah ('cool',) man
```

Si nous n'avons pas de `*args` à placer, on peut mettre `*` toute seule:

```python
def poule(normal, *, keyword_only):
    print(normal, keyword_only)

>>> poule("yeah", "cool")
TypeError: poule() takes 1 positional argument but 2 were given
>>>
>>> poule("yeah", keyword_only="cool")
yeah cool
```

# Bonus (python 3.5)

On peut faire de l'unpacking directement dans les littéraux:

```python
>>> l = ["poule", "cochon", *range(3)]
>>> print(l)
['poule', 'cochon', 0, 1, 2]
```

On peut utiliser plusieurs fois l'unpacking des arguments dans un même appel et dans ce cas nous utilisons plusieurs `*` dans les paramètres:

```python
def poule(a, b, c, d):
    print(a, b, c, d)

>>> couleurs = ('vert', 'rouge')
>>> poule(*couleurs, *range(2))
vert rouge 0 1
```
