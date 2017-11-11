---
layout: post
title: "Py: Détails d'implémentations du langage"
subtitle: "Under the hood"
date: 2017-11-11
author: Sol
category: Py
tags: 
finished: true
mathjax: true
---

# En vrac

* Toute valeur en Python est un objet
* En Python toutes les variables sont des références vers des objets
* Il n'y a pas de **vrai** constantes en Python! On parle de constante quand une variable est écrite toute en majuscules (**convention**) et qu'elle est **sensé** ne jamais changer.

# Duck typing

**Un objet en Python est défini par sa structure (les attributs qu'il contient et les méthodes qu'il lui sont applicables) plutôt que par son type.**

Python est entièrement construit autour de cette idée appelée _**duck-typing**_: 

> "Si je vois un animal qui vole comme un canard, cancane comme un canard, et nage comme un canard, alors j'appelle cet oiseau un cannard" (James Whitcomb Riley)

# Python / CPython, Jython ...

Python est **un langage**, un série de regles qui servent à écrire un programme et il existe plusieurs **implémentation de ce langage**.

Peu importe le choix de l'implémentation qu'on utilise, elles font toutes à peu près la même chose, c'est à dire interpréter le texte qui forme le code de notre programme et l'interprete en executant les instructions.

> ATTENTION A LA CONFUSION ! Aucunne implémentation ne compile le code en C ou dans aucun autre langage !

CPython est le nom de l'implémentation originale de Python **qui est écrite** en C (d'où la confusion). Le "C" dans CPython fait référence au langage utilisé pour écrire l'interpeteur Python.

* **CPython**: interpreteur original, écrit en C
* **Jython**: est le **même langage** mais implémenté (interpeteur écrit en...) en utilisant Java.
* **IronPython**: est le **même langage** mais implémenté en utilisant C#.

Pour ajouter une couche, et boucler la boucle, **PyPy** est un interpreteur Python écrit en... Python.

* [Plus d'info, entre autres sur le byte-code](https://stackoverflow.com/questions/17130975/python-vs-cpython)


# Data-model

_Traduction de la [doc officielle](https://docs.python.org/fr/3/reference/datamodel.html)_

## Objets: Identité, type et valeur

Les _objets_ sont l'abstraction de Python des données. Toute donnée dans un programme Python est représentée par des objets ou par des relations entre des objets.

Chaque objet a une **identitée**, **un type** et une **valeur**. 

L'**identitée** d'un objet ne change jamais après sa création. On peut voir l'identité comme l'adresse en mémoire de l'objet. L'opérateur `is` compare les identitées de deux objets et la fonction `id()` retourne une représentation entière (un int) de l'identité d'un objet.

> En CPython, `id()` retourne l'adresse mémoire (sous forme entière et non pas hexadécimale).

```py
>>> objet = 5
>>> print(id(objet))
1783169616
>>> print(hex(id(objet)))
0x6a490250
```

> `0x6a490250` est l'adresse en mémoire et `1783169616` est la même adresse sous forme entière.

Le **type** d'un objet détermine les opérations qu'un objet supporte (ex. "est-ce-qu'il a une longueur?") et définit également les valeurs possibles pour les objets de ce type. La fonction `type()` retourne le type d'un objet (qui est lui même un objet (le type retourné est un objet)). De la même façon que l'identité d'un objet, le type d'un objet ne change jamais.

La **valeur** de certains objets peut changer. Un objet dont la valeur peut changer est dit <span style="color:green"> **mutable** </span> par opposition à un objet dont la valeur ne peut plus changer après sa création qui lui est dit <span style="color:green"> **immutable** </span>.

Certains objets contiennent des références vers d'autres objets. Ces objets sont appelés des **conteneurs** (tuples, lists, dictionnaires,...). 

> La valeur d'un objet conteneur **immutable** qui contient une référence sur un objet **mutables** peut changer si la valeur de l'objet dont il contient la référence change. Pourtant, le conteneur est quand même concidéré immutable car la collection d'objet qu'il contient ne peut être changée. Donc **l'immutabilité n'est pas strictement la même chose que le fait d'avoir une valeur inchangeable**, c'est plus subtile que ça d'où la nuance dans le choix du terme.

La mutabilité d'un objet est déterminée par son type; par exemple, les _int_, les _strings_ et les _tuples_ sont immutable, alors que les _dictionnaires_ et les _listes_ sont mutable.

Le type d'un objet affecte presque tous les aspects du comportement d'un objet, même l'importance de son identité (adresse en mémoire). Pour les types immutables, les opérations qui calculent de nouvelles valeurs **peuvent** retourner une référence sur un objet du même type et de même valeur (ce n'est pas permis pour les objet mutables). Par exemple après:

```py
a = 1
b = 1
```

`a` et `b` peuvent (ou pas) référencer le même objet de valeur `1` (dépend de l'implémentation) alors qu'après:

```py
c = []
d = []
```

il est garantit que `c` et `d` référenceront deux différentes, uniques  listes nouvellement créées.

> Noter que `c = d = []` assigne le même objet à `c` et `d`.


## Destruction

Les objets ne sont jamais explicitement détruits. Une fois qu'il n'y a plus aucune référence sur eux et sont donc inatteignables, ils **peuvent** être _garbage-collected_. La gestion du garbage collector est laissé à l'implémentation utilisée et n'est pas gérée par le langage lui même. L'implémentation a le droit de retarder la garbage collectin ou même de l'omettre complètement. 

> CPython utilise <span style="color:red"> pour le moment </span> un système de garbage collection à base de comptage des références avec (optionnellement) une détection retardée les liens cycliques (_références circulaires?_) qui collecte **la majorité** des objets aussi tot qu'ils deviennent inatteignables, mais ne garantit pas la collecte des garbages contenant des références circulaires.

Certains objets contiennent des références à des ressources externes comme des fichiers ou des fenetres. Il est entendu que ces ressources sont libérées quand l'objet est garbage-collected, mais comme cette dernière n'est pas garentie à 100%, les objets qui peuvent contenir ce genre de références externes fournissent une méthode pour explicitement libérer ces ressources, généralement une méthode `close()`

> Il est fortement recommandé d'utiliser ces methodes ! Les déclaration tel que `try..finally` et `with` peuvent ici être très utiles.



## TEMPORAIRE EN ATTENDANT LA SUITE DE LA TRADUCTION

Un **itérable** est un objet sur lequel on peut itérer. En gros n'import quel objet sur lequel on peut appeler la construction `for i in objet`.

Les objets itérables ont trois sous-types:
* **Sequence** (listes, tuples, str): c'est un objet itérable à accès direct. On peut à l'aide des `[]` appeler un élément situé à un indexe précis, ou slicer une partie de cette séquence `seq[de:à]`.

* **maps** (dictionnaires): Si l'accès se fait via une clé à la place d'un indexe c'est un **mapping**. Une clé unique qui référence un élement.

* **Générateurs**: Le plus basique des 3. Il ne supporte pas de d'accès direct et donc pas de slicing non plus. Les générateurs créent les éléments qu'ils "contiennent" au fur et à mesure qu'on itère dessus,d'où leur nom. Généralement créés à partir d'une generator comprehension (de la même façon qu'une list compregension mais avec des `()` à la place des `[]`) ou si une fonction contient le mot clé `yield` dans son corp, elle retournera un générateur.

Le dénominateur commun de tous les itérables est l'`iterator`. Les **itérateurs** ont la même interface que le type le plus basique qu'ils supportent (le générateur). Il sont créés explicitement par l'appel de la méthode `__iter__()` sur un iterable...

```py
>>> l = [2, 7, 8, 4]
>>> it = l.__iter__()
>>> type(it)
<class 'list_iterator'>
>>> print(it)
<list_iterator object at 0x00000219BC473320>
>>> print(it.__next__())
2
```

... et sont créés implicitement dans toutes les boucles.