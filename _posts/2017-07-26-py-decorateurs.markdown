---
layout: post
title: "Py: Décorateurs"
subtitle: "introduction aux décorateurs Python"
date: 2017-07-25
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

#### sources

* [sam&max](http://sametmax.com/comment-utiliser-yield-et-les-generateurs-en-python/)

## Introduction

Les décorateurs sont des **wrappers**, c'est à dire qu'ils permettent d'exécuter du code avant et après la fonction qu'ils décorent, **sans modifier la fonction elle-même**.
Pour comprendre cela il faut prendre conscience de ce qu'implique le fait qu'une fonction soit un objet.

```python
>>> def crier(mot="yes"):
...     return mot.capitalize() + "!"

>>> print(crier())
Yes!
```

Les fonctions sont des **objets** et peuvent être assignées à une variable:
> À noter qu'on utilise **pas les parenthèses**. La fonction n'est pas applée. Ici nous mettons la fonction `crier` dans la vriable `hurler` afin de pouvoir appeler `crier` avec `hurler`

```python
>>> hurler = crier
>>> print(hurler())
Yes!
```

On peut également supprimer l'ancien nom `crier`, la fonction reste accessible avec `hurler`:

```python
>>> del crier
>>> try:
...     print(crier())
...
>>> except NameError as e:
...     print(e)
...
name 'crier' is not defined
>>>print(hurler())
Yes!
```

Une fonctions peut être définie dans une autre fonction.
Ici, on appelle `parler`, qui définit `chuchoter` à chaque appel, puis `chuchoter` est appelé à l'intérieur de `parler`:

```python
>>> def parler():
...     def chuchoter(mot="yes"):
...         return mot.lower() + "..."
...     print(chuchoter())
...
>>> parler()
yes...
```

Mais `chuchoter` n'existe pas endehors de `parler`:

 ```python
>>> try:
...     print(chuchoter())
...
>>> except NameError as e:
...
>>> print(e)
name 'chuchoter' is not defined
```

## Retour et Passage de fonctions par référence

Le fait que les fonctions peuvent être...
* assignées à une variable
* définies dans une autre fonction...

... implique qu'une fonctoin peut retourner une autre fonction:

> noter l'absence de `()` pour le retour. On appelle pas la fonction, on retourne **l'objet fonction**.

```python
def creerParler(specification="crier"):
    def crier(mot="yes"):
        return mot.capitalize() + "!"

    def chuchoter(mot="yes"):
        return mot.lower() + "..."

    if specification == "crier":
        return crier
    else:
        return chuchoter
```

Obtenir la fonction et l'assigner à une variable:

```python
>>> parler = creerParler()
```

`parler` est une variable qui contient la fonction `crier`:

```python
>>> print(parler)
<function creerParler.<locals>.crier at 0x7f073ea62b70>
```

On peut appeler `crier` depuis `parler`:

```python
>>> print(parler())
Yes!
```

Même chose avec la fonction `chuchoter` et sans utiliser de variable:

```python
>>> print(creerParler("chuchoter"))
<function creerParler.<locals>.chuchoter at 0x7f9e743a4ea0>
>>> print(creerParler("chuchoter")())
yes...
```

Si on peut retourner une fonction, on peut églement en passer une en argument:

```python
>>> def faireQqchAvant(fonction):
...     print("avant l'appel de la fonction")
...     print(fonction())
...
>>> faireQqchAvant(hurler)
avant l'appel de la fonction
Yes!
```

## Décorateur fait maison

Un decorateur est une fonction qui attend une autre fonction en parametre:

```python
def decorateur_perso(fonction_a_decorer):

    # En interne, decorateur_perso definit une
    # fonction a la volee: le wrapper.
    # Ce dernier va enrober la fonction originale
    # de telle sorte qu'il puisse executer du
    # code avant et pres celle-ci
    def wrapper():

        # Code qui s'execute AVANT que la 
        # fonction a decorer ne s'execute
        print("Avant que la fonction ne s'execute")

        # Appel de la fonction (avec les parentheses donc)
        fonction_a_decorer()

        # Code qui s'execute APRES que la
        # fonction a decorer se soit executee
        print("Apres l'execution")
    
    # Ici, la fonction a decorer N'A JAMAIS ETE EXECUTEE.
    # On retourne le wrapper (sans les parentheses donc) 
    # que l'on vient de creer.
    # Le wrapper contient la fonction originale et le code
    # a executer avant et apres, pret a etre utilise.
    return wrapper
```

Maintenant nous creons une fonction que l'on ne souhaite pas modifier.

```python
def fonction_intouchable():
    print("Je suis une fonction intouchable !")

fonction_intouchable()
```

**output**:

```
Je suis une fonction intouchable !
```

Grace a `decorateur_perso`, nous pouvons étendre le comportement de la `fonction_intouchable`. Il suffit de la passer en argument au `décorateur_perso` qui va alors l'enrober dans le code que l'on souhaite, pour ensuite retourner une nouvelle fonction:

```python
fonction_intouchable_decoree = decorateur_perso(fonction_intouchable)
fonction_intouchable_decoree()
```

**output**:

```
Avant que la fonction ne s'execute
Je suis une fonction intouchable !
Apres l'execution
```

Nous pouvons également faire en sorte qu'à chaque fois qu'on appelle `fonction_intouchable`, ce soit `fonction_intouchable_decoree` qui soit appelée. Il suffit d'écraser la fonction originale par celle retournée par le `décorateur_perso`:

```python
fonction_intouchable = decorateur_perso(fonction_intouchable)
fonction_intouchable()
```

**output**:

```
Avant que la fonction ne s'execute
Je suis une fonction intouchable !
Apres l'execution
```

Voila ce que font les décorateurs.

## Décorateur built-in

En reprenant l'exemple précédent, en utilisant la syntaxe précédente:

```python
@decorateur_perso
def fonction_intouchable():
    print("Me touche pas !")

fonction_intouchable()
```

**output**:

```
Avant que la fonction ne s'execute
Je suis une fonction intouchable !
Apres l'execution
```

`@decorateur_perso` est simplement un raccourcis pour  
`fonction_intouchable = decorateur_perso(fonction_intouchable)`  

Les décorateurs sont juste une **variante pythonique** du classique _patern design_ **décorateur**.

Nous pouvon cumuler ces derniers (syntaxe nulle):

```python
def pain(func):
    def wrapper():
        print("</''''''\>")
        func()
        print("<\______/>")
    return wrapper

def ingredients(func):
    def wrapper():
        print("0tomates0")
        func()
        print("~salade~")
    return wrapper

@pain
@ingredients
def sandwitch(food="--jambon--"):
    print(food)

sandwitch()
```

**output**:

```
</''''''\>
0tomates0
--jambon--
~salade~
<\______/>
```

L'ordre d'application des décorateurs est important:

```python
@ingredients
@pain
def sandwitch_nul(food="--jambon--"):
    print(food)

sandwitch_nul()
```

**output**:

```
0tomates0
</''''''\>
--jambon--
<\______/>
~salade~
```