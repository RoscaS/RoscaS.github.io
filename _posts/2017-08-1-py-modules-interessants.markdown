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

## inspect

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


