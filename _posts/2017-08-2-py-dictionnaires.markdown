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

## Introduction

Les Dictionnaires de Python sont une structure de donnée de type **non-linéaire** à acces **direct** qui est dite _associative_. Elle stock des paires clé-valeur. Ce type de structure de données porte plusieurs noms: Keyed list, associative array, hash table.

* La clé associée à une valeur est obligatoirement de type **string**. 
* La valeur peut être de n'importe quel type.

### Création

`dico = {'clé1': val1, 'clé2': val2, 'clé3': val3}`

```python
>>> dico = {'a': 1, 'b': 2, 'c': 3}
>>> print(dico)
{'b': 2, 'c': 3, 'a': 1}
```

### Ajout

`dico['nouvelle_clé'] = nouvelle_valeur`

```python
>>> dico['d'] = 4
>>> dico['e'] = 5
>>> print(dico)
{'d': 4, 'a': 1, 'b': 2, 'c': 3, 'e': 5}
```

### Valeur d'une clé spécifique

`dico['clé']`

```python
>>> print(dico['b'])
2
```

### Supprimer une entrée 

`del dico['clé']`

```python
>>> del dico['b']
>>> print(dico)
{'e': 5, 'd': 4, 'c': 3, 'a': 1}
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

### 

```python

```