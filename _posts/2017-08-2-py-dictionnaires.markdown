---
layout: post
title: "Py: Dictionnaires"
subtitle: ""
date: 2017-08-01
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

## Méthodes

* `len(d)` $$ \Rightarrow $$ retourne le nombre d'objets dans le dico `d`

#### Get, Set, Delete

* `d[k]` $$ \Rightarrow $$ retourne la valeur de la **clé** $$k$$ _if_ existe, _else_ <span style="color:red"> KeyError</span>
* `d.get(k)` $$ \Rightarrow $$ _return_ la valeur de la **clé** $$k$$ _if_ existe, _else_ return `None`
* `d.get(k, v)` $$ \Rightarrow $$ _return_ la valeur de la **clé** $$k$$ _if_ existe, _else_ return $$v$$
* `d[k] = v` $$ \Rightarrow $$ _if_ $$k$$ existe, remplace la valeur à la **clé** $$k$$ _else_ crée nouvelle entré
* `del d[k]` $$ \Rightarrow $$ supprime la clé/valeur de la **clé** $$k$$ _if_ existe, else <span style="color:red"> KeyError </span> 

#### Check existence
* `k in d` $$ \Rightarrow $$ `True` _if_ $$d$$ a une clé $$k$$ _else_ `False` 
* `k not in d` $$ \Rightarrow $$ `True` _if_ $$d$$ n'a pas une clé $$k$$ _else_ `False` 

#### Get all keys, values, keys/values

* `d.keys()` $$ \Rightarrow $$ repr de toutes les clés
* `d.values()` $$ \Rightarrow $$ repr de toutes les valeurs
* `d.items()` $$ \Rightarrow $$ repr (tuples) de toutes les clé/valeurs

#### Pop

* `d.pop(k)` $$ \Rightarrow $$ supprime et _return_ la valeur _if_ **clé** $$k$$ exist, _else_ <span style="color:red">KeyError</span>.
* `d.pop(k,v)` $$ \Rightarrow $$ supprime et _return_ la valeur _if_ **clé** $$k$$ _else_ _return_ $$v$$
* `d.popitem()` $$ \Rightarrow $$ supprime et _return_ une random clé/valeur _if_ $$d$$ vide <span style="color:red"> KeyError </span> 


#### Set value

* `d.setdefault(k)` $$ \Rightarrow $$ _if_ **clé** $$k$$ existe _return _ valuem _else_ ajoute la clé $$k$$ avec valeur `None`
* `d.setdefault(k, v)` $$ \Rightarrow $$ _if_ **clé** $$k$$ existe _return _ valuem _else_ ajoute clé $$k$$ avec valeur $$v$$

#### Clear, copy

* `d.clear()` $$ \Rightarrow $$ supprime tout
* `d.copy()` $$ \Rightarrow $$ _return_ shallow copy de $$d$$



## Introduction

Les structures de données comme les listes sont indexées à l'aide d'un nombre, dans certains cas il peut être interessant d'accéder à une valeur par un indexe qui est une string par exemple. Les routes portent souvent des noms comme "n23" ou "e17". Une structure de données qui donne des informations sur ces dernières devrait pouvoir nous retourner ces informations si on recherche le nom (clé) de ces routes.

Les Dictionnaires de Python sont une structure de donnée de type **non-linéaire** à acces **direct** qui est dite _associative_. Elle stock des paires clé-valeur. Ce type de structure de données porte plusieurs noms: Keyed list, associative array, hash table. En Python cette structure de données s'appelle `dict`. Cette structure de données est **dynamique**.

* La clé associée à une valeur est obligatoirement être **immutable** (int, float, string, tuples). 
* La valeur peut être de n'importe quel type.

```python
dico = {}

dico[1]        = 'cochon'
dico["poule"]  = [1,2,3]
dico[(1,4)]    = {1:10, 2:20, 3:30}

print(dico[1])
print(dico["poule"])
print(dico[(1,4)])
```

**output**:

```
cochon
[1, 2, 3]
{1: 10, 2: 20, 3: 30}
```

Exemple simple de fonctionnement d'un dictionnaire:

```python
capitales = {}

capitales['Belgique'] = 'Bruxelles'
capitales['France']   = 'Paris'
capitales['Russie']   = 'Moscou'
capitales['Roumanie'] = 'Bucarest'

pays = ['Russie', 'Suisse', 'Belgique', 'Hollande']

for i in pays:
    # check si le pays est dans le dico
    if i in capitales:
        print("La capitale de la {} est {}".format(
            i, capitales[i]
        ))
    else:
        print("La capitale de la", i, "est inconnue")
```

**output:**

```python
La capitale de la Russie est Moscou
La capitale de la Suisse est inconnue
La capitale de la Belgique est Bruxelles
La capitale de la Hollande est inconnue
```

Chaque élément du dictionnaire est composé de deux objets, une **clé** et une **valeur** (key/value). Dans cet exemple la clé est le pays et la valeur la capitale.
<span style="color:red">Les clés identifient les éléments d'un dictionnaire.</span> On peut voir la clé comme l'indexe de la valeur associée à cette clé. Un même dictionnaire ne peut pas comporter deux clés identiques. De la même façon qu'un dictionnaire papier ou d'un annuaire téléphonique, qui identifie une valeur par un identifiant unique.

## Quand utiliser un dictionnaire

* Pour compter des choses. Dans ce cas, il nous faut un dictionnaire où les clés sont des objets et les valeurs des quantités.
* Stocker une donnée **associée** à un objet. Les clés sont des objets et la valeur est la donnée associée à ces objets.


Exemple d'utilisation d'une clé qui est un objet autre que les types primitifs:

```python
class Point(object):

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def pos(self):
        return "({},{})".format(self.x, self.y)


a = Point(1,2)
b = Point(3,4)
c = Point(5,6)


pixels = {}

pixels[a] = ['rouge', 1]
pixels[b] = ['vert',  1]
pixels[c] = ['noir',  0]


for i in pixels:
    print("Position: {}\nCouleur: {}\nStatus: {}\n".format(
        i.pos(), pixels[i][0], pixels[i][1]
    ))
```

**output**:

```
Position: (5,6)
Couleur: noir
Status: 0

Position: (1,2)
Couleur: rouge
Status: 1

Position: (3,4)
Couleur: vert
Status: 1
```

## Différente façons de créer un dictionnaire

Utile uniquement dans le cas de petits dictionnaires:

```python
Capitals = {'Russia': 'Moscow', 'Ukraine': 'Kiev', 'USA': 'Washington'}
```

Pareil mais dans ce cas nous passons les clés sont passées comme des paramètres nominatifs de `dict`. Dans ce cas, **les clés ne peuvent être que des strings**!

```python
Capitals = dict(Russia = 'Moscow', Ukraine = 'Kiev', USA = 'Washington')
```

Ici nous passons une liste de tuples de deux éléments `(clé, valeur)`:

```python
Capitals = dict([("Russia", "Moscow"), ("Ukraine", "Kiev"), ("USA", "Washington")])
```

Finalement, via la fonctoin zip qui prend en paramètres deux listes de taille identique: Une liste de clés et une liste de valeurs.

```python
Capitals = dict(zip(["Russia", "Ukraine", "USA"], ["Moscow", "Kiev", "Washington"]))
```


## Supression d'un élément

Pour supprimer un élément, nous pouvons utiliser `del dico[clé]`. <span style="color:red"> Cette façon de faire lève une exception `KeyError` si la clé n'existe pas.</span>

Une autre façon de faire est avec la méthode `pop`: `dico.pop(clé)`. Cela retourne la valeur de l'élément retiré et <span style="color:red">si l'élément n'existe pas, une exception est également levée </span>. Si nous fournissons un second paramètres, si l'élément à supprimer n'existe pas, à la place de lever une exception, la méthode retourne le second paramètre:

```python
>>> dico = {'a': 2, 'b': 7}
>>> dico.pop('z', -1)
-1
```

## Itérer sur tous les éléments

On peut simplement itérer sur tous les éléments de cette façon:

```python
>>> dico = dict(zip('abcdef', list(range(6))))

>>> for key, val in dico.items():
...     print(key, val)
a 0
d 3
e 4
c 2
b 1
f 5
```

## Représentation interne:

Les méthodes `keys()`, `values()` et `items()` retourne la représentation (__repr__) du dictionnaire. Ce sont des objets similaires au sets mais mutables (si on change une valeur associée à une clé, la valeur change).

* `keys()` retourne la représentation de toutes les clés
* `value()` retourn la représentation de toutes les valeurs
* `items()` retourn la représentation de toutes les paires clé-valeur (des tuples).

```python
dico = dict(zip('abcdef', list(range(6))))
print(dico.keys())
print(dico.values())
print(dico.items())
```

**output**:

```
dict_keys(['e', 'b', 'd', 'c', 'f', 'a'])
dict_values([4, 1, 3, 2, 5, 0])
dict_items([('e', 4), ('b', 1), ('d', 3), ('c', 2), ('f', 5), ('a', 0)])
```


## Exemples d'utilisation

### Création

`dico = {'clé1': val1, 'clé2': val2, 'clé3': val3}`

```python
>>> dico = {'a': 1, 'b': 2, 'c': 3}
>>> print(dico)
{'b': 2, 'c': 3, 'a': 1}
```

### Ajout

`dico[nouvelle_clé] = nouvelle_valeur`

```python
>>> dico['d'] = 4
>>> dico['e'] = 5
>>> print(dico)
{'d': 4, 'a': 1, 'b': 2, 'c': 3, 'e': 5}
```

### Valeur d'une clé spécifique

`dico[clé]`

```python
>>> print(dico['b'])
2
```

`dico.get(clé)`

```python
>>> print(dico.get('a'))
1
```

`dico.get(clé, value)`
Retourne `value` si la clé n'existe pas. Utile pour retourner un code erreur par exemple.

```python
>>> print(dico.get('z'), -1)
-1
```

### Supprimer une entrée 

`del dico[clé]`

```python
>>> del dico['b']
>>> print(dico)
{'e': 5, 'd': 4, 'c': 3, 'a': 1}
```

### Supprimer (safe)

`dico.pop(clé)`. Retourne la valeur de l'élément retiré ou lève une exception si l'élément n'existe pas.

Si nous fournissons un second paramètres, si l'élément à supprimer n'existe pas, à la place de lever une exception, la méthode retourne le second paramètre:

```python
>>> dico = {'a': 2, 'b': 7}
>>> dico.pop('z', -1)
-1
```


### Retourner toutes les clés 

`dico.keys()`

```python
>>> print(dico.keys())
dict_keys(['d', 'a', 'c', 'e', 'b'])
>>> print(type(dico.keys()))
<class 'dict_keys'>
```

### Retourner toutes les valeurs

`dico.values()`

```python
print(dico.values())
dict_values([4, 3, 2, 5, 1])
print(type(dico.values()))
<class 'dict_values'>
```

### Vérifier si une clé existe:

`clé in dico`

```python
>>> print('a' in dico)
True
>>> print('z' in dico)
False
>>> print('a' not in dico)
False
```



### 

```python

```

### 

```python

```

### 

```python

```