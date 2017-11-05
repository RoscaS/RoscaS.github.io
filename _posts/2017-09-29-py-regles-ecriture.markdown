---
layout: post
title: "Py: Regles d'écriture "
subtitle: "Pour un code Pythonique"
date: 2017-09-29
author: Sol
category: Py
tags: tools
finished: true
mathjax: true
---

## Sources

* [zestdesavoir](https://zestedesavoir.com/articles/1079/les-secrets-dun-code-pythonique/)

# Grands acronymes

## KISS (Keep it simple, stupid)
Le code doit toujours rester le plus simple possible, afin de rester lisible pour les autres contributeurs.

Cela s'illustre par la syntaxe même du langage, qui comprendpeu de constructions différentes, mais doit aussi se retrouver dans le code produit. Les fonctions, par exemple, doivent êetre dédiées à une unique fonctionnalité, de même pour les classes et leurs méthodes.

En parlant de classes, **il est inutile de créer de nouvelles classes trop vite**, là ou les types primitifs du langge pourraient répondre au besoin. Par exemple, pour un objet qui ne contiendrait que des données, associées à aucune méthode, un dictionnaire fait très bien l'affaire.

```py
user = {
    'username': 'Poule',
    'realname': 'Claudie',
    'password': '12345'
}
```

Le principe s'exprime aussi par le fait de ne pas créer de hiéarchie de classes trop complexe, et même d'ailleurs de **ne pas utiliser d'héritge quand ce n'est pas nécessaire** (penser au _Duck typing_). De même, Python dispose d'outils puissants (décorateurs, générateurs, métaclasses), qui doivent être utilisés judicieusement, quand ils ne nuisent pas à la simplicité.

## DRY (Don't Repeat Yourself)
Cette règle a pour but d'éviter la redondance. Le code dupliqué est plus difficile à maintenir, car chaque modification doit être répercutée sur toutes les occurences du code.

**La répétition peut se comprendre** à petite échelle: par exemple **une même ligne répéte à deux endroits du code**. Au-delà, une factorisation est necessaire, afin de dédier une fonction à ce comportement.

```py
import sys, random

def errlog(template, *args):
    print(template.format(*args), file=sys.stderr)

secret = random.randint(0, 100)
guess = int(input('Nbr btwin 0 and 100: '))

if guess < secret:
    errlog('Nombre {} trop petit', guess)

if guess > secret:
    errlog('Nombre {} trop grand', guess)
...
```

> La fonction `errlog` permet ici de factoriser le formatage et l'affichage de messages d'erreur.

## YAGNI (Tou Ain't Gonna Need It _mate_)
Ce principe est plus une ligne de conduite pour le processus de développement. Il est inutile de développer maintenant une fonctionnalité qui ne servira peut-être jamais. Il est préférable de s'attaquer d'abord à ce qui est actuellement nécessaire.

Développer une fonctionnalité trop tot présente de plus d'autres problèmes:

* Inutilisée, elle restera inconnue des autres développeurs
* Si elle vient à êetreutilisée, elle ne le sera peut-être pas dans les termes actuellement définis
* La fonctionnalité devra être continuellement testée tout le long du developpement du projet, et potentiellement déboguée
* Enfin, elle pourrait entrer en conflit avec d'autres fonctionnalités requises.

## We're all consenting adults here
Ou plus clairemen, les développeurs sont conscients et responsables de leurs actes. Cela s'illustre par **la manière de protéger des attributs** en Python, en les préfixant par un `_`.

En soi, rien n'empêche d'accéder depuis l'extérieur à tel attribut mais le préfixe signale au développeur qu'il accède à un état interne, que sa modification pourrait compromettre le comportement normal de l'objet, et qu'il le fait donc en connaissance de cause.

```py
class MyObject(object):
    def __init__(self):
        self._internal = 'internal state'

obj = MyObject()
print(obj._internal)
```

En parlant d'attributs, **on préférera toujorus en Python un accès direct aux attributs plutot que des méthodes** _getter/setter_. Quitte à passer par des propriétés s'il est nécessaire que la récupération ou la modification de l'attribut soit dynamique.

## Easier to ask forgiveness than permission (EAFP)
Python fait partie des langages qui considèrent qu'il est plus simple d'essayer puis de gérer les erreurs que de demander la permission en amont.

Pour gérer l'ouverture d'un fichier, par exemple, on préférera faire appel à `open`, et traiter les différentes exceptions qui pourraient se produire (fichier inexistant, droits insuffisants, etc...), plutot que de tester une à une ces différentes conditions.

```py
try:
    with open('filemame', 'r') as f:
        handle_file(f)

except FileNotFoundError as e:
    errlog('Fichier {!r} non trouvé', e.filename)
except PermissionError as e:
    errlog('Fichier {!r} non lisible', e.filename)
```

Cette manière de procéder a aussi l'avantage d'être plus sure en Python. En effet, dans le cas où l'on testerait d'abord l'existence du fichier, rien nenous grentit qu'il serait toujours présent au moment de l'ouverture proprement dite (il peut être supprimé par un utre progrmme entre temps).

> Ce principe s'oppose au LBYL (Look before you leap) préconisé par d'autres langages comme le C.

# Mécanismes du langage

On reconnait généralement un bon code Python à l'utilisation des mécanismes qui lui sont propres.

## Unpacking
L'unpacking (ou déconstruction) est une technique qui permet l'assignation de plusieurs variables en une seule instruction. Par exemple pour échanger les valeurs de deux variables:

```py
>>> a = 5
>>> b = 2
>>> a, b = b, a
>>> print(a, b)
2 5
```

En interne, lors de la 3e ligne, Python crée un tuple `(b, a)`, qui est ensuite déconstruit et son contenu est stocké dans les variables `a` et `b`

L'unpacking ne se limite pas à ça, il permet aussi de déconstruire des structures imbriquées (tuples, listes, strings, dictionnaires).

```py
>>> l = [0, (1, 2, {3: 'foo', 4: 'bar}), 5]
>>> a, (b, c, (d, e)), f = l
>>> print(a, b, c, d , e, f)
0 1 2 3 4 5
>>> x, y, z = 'bar'
>>> print(x, y, z)
b a r 
```

L'unpacking est une manière élégante de séparer les éléments d'une liste, il est donc courant de l'employer en Python.

> constructions plus complexes avec l'opérateur _splat_ (`*`), voir article associé

## Conditions
Toute valeur en Python peut s'évaluer sous forme d'un booléen, il n'est donc pas nécéssaire de la convertir préalablement. Les valeurs `None`, `0` et les conteneurs vides (`''`, `()`, `[]`, `set()`, etc...) s'évaluent à `False`. Les autres nombres, les conteneurs non vides, et plus généralement toute valeur qui n'est pas explicitement fausse s'évaluent à `True`.

**L'usage de ternaires** est a privilégier quand on souhaite évaluer des expressions conditionnelles courtes:

```py
name = user.name if user is not None else 'anonymous'
```

> On notera l'utilisation de l'opérateur `is` pour la comparaison avec `None`. Ce dernier étant une constante unique, `is` permet d'en assurer la singularité.

## Boucle for
En Python, la boucle `for` doit toujours être privilégiée pour itérer sur un ensemble d'éléments. Si on utilise une `while` **pour itérer**, c'est probablement qu'il y a un problème de conception. Cet ensemble d'éléments ne prend pas toujours la forme d'une liste, il peut s'agire d'un dictionnaire, d'un fichier, d'un intervalle de nombres (`range`).

Ceci est valable pour toutes les variables qui devraient prendre des valeurs successives à chaque itération. Ainsi, on s'orientera vers `zip` pour itérer sur plusieurs éléments à la fois, vers `enumerate` pour itérer en gardant trace de l'index dans la liste, ou encore vers des constructions plus complexes du mondule `itertools`.

```py
names = ['Alex', 'Alice', 'Bob']
ages = [45, 27, 74]

for name, age in zip(names, ages):
    print(name, age)

for i, (name, age) in enumerate(zip(names, ages)):
    print(i, name, age)
```

On retrouve dans cette construction l'unpacking abordé plus haut, qui peut donc s'utiliser aussi pour les boucles `for`.

## Listes en intension (comprehension)
Outre la boucle `for`, le protocole d'itération est aussi représenté par les listes en intension, qui doivent être utilisées dès que possible, **tant qu'elles ne nuisent pas à la lisibilité du code**.

Pour construire la liste des carrés des nombres de 0 à 9, on utilisera le code suivant, plutot qu'une boucle multi-lignes et un remplissage de liste manuel:

```py
squarres = [i**2 for i in range(10)]
```

On retrouve la même construction pour les dictionnaires en intension:

```py
squares_set = {i**2 for i in range(10)}
squares_dict = {i: i**2 for i in range(10)}
```

## Générateurs
Un autre mécanisme est celui des générateurs (et des générateurs en intension), à utiliser quand il n'est pas nécéssaire d'avoir une représentation complète d'un ensemble en mémoire. Si notre liste `squares` a simplement pour but de calculer la somme des éléments (`sum(squares)`), nous lui préférerons la version utilisant un générateur, évitant ainsi le stockage inutile de la liste:

```py
sum_squares = sum(x**2 for x in range(10))
```

## EXceptions
La gestion d'erreurs est réalisée en Python à l'aide d'un mécanisme d'exceptions, mais les exceptions ne se limitent pas à cela. Le protocole d'itération décrit plus haut s'appuie par exemple sur une exception `StopIteration` levée en fin de boucle.

Les traitements défectueux doivent toujours remonter une exception adaptée au problème, et décrivant au mieux sa raison. Les types d'exceptions sont généralement hiérarchisés de façon à représenter le problème à différents niveaux d'abstraction.

Si on est pas exemple amennés à développer une bibliothèque, il est courant que toutes ses exceptions héritent d'une même base permettant facilement d'attraper toutes les erreurs de la bibliothèque. Dnas le cas d'un champ manquant lors de l'analyse du fichier de configuration d'un composant de la bibliothèque, `mylib`, on pourrait avoir une exception du type `mylib.FieldMissingError` héritant de `mylib.ParseError` et elle même de `mylob.Error`

De l'autre coté, il est conseillé d'ttraper judicieusement les exceptions. Si on souhaite attraper l'exception `mylib.FieldMissingError` plutot que `mylib.Error` qui serait dans ce cas trop général.

## Décorateurs
Les décorateurs, utilisés à bon escient, sont aussi une particularité du langage. On reconnait un bon code idiomatique à l'utilisation des décorateurs de la bibliothèque standard (staticmethod, classmethod, property).

```py
class Circle(object):
    def __init__(self, cx, cy, radius):
    self.cx, self.cy = cx, cy
    self.radius = radius

    @classmethod
    def from_diameter(cls, ax, ay, bx, by):
        cx, cy = (ax + by) / 2, (ay + by) /2
        diam = ((ax - bx)**2 + (ay - by)**2)**0.5
    
    @property
    def area(self):
        return math.pi * self.radius**2
```

**La définition de ses propres décorateurs ne doit en revanche avoir lieu que si elle permet un gain net en lisibilité par rapport aux autres solutions envisagées.

## Gestionnaires de contextes

L'ouverture de fichiers, doit toujours passer par l'utilisation d'un bloc `with`. Ce bloc permet en effet d'automatiser des opérations de libération des ressources, et ce en tous les cas (déroulement normal ou erreur).

## Modules collection
Ce module comporte d'autres structures de données essentielles au langage: `OrderedDict`, `nametuple`, `counter`, ou encore `defaultdict` qui sera préférable à une utilisation systématique de `setdefault`. Une réflexe classique est de vouloir recréer ces classes, alors qu'elles sont à portée de main.

```py
>>> from collections import Counter
>>> names = ['Alice', 'Bob', 'Bob', 'Alice', 'Alex', 'Bob']
>>> count = Counter(names)
>>> count
Counter({'Bob': 3, 'Alice': 2, 'Alex': 1})
>>> count['Alice']
2
>>> count['Camille']
0
```

# Les bons réflexes

En premier lieu, il faut bien sur se relire en faisant attention aux divers principes et règles énoncés. Voire se faire relire par un tiers lorsque cela est possible.

Un autre réflexe sera de s'imprégner de la bibliothèque standard, et de s'assurer pour chaque fonctionnalité que l'on s'apprête à implémenter que celle-ci n'y existe pas déjà.

La fonction `help` permet aussi d'obtenir plus d'informations sur un module, un type, une fonction ou même un mécanisme du langage. Elle offre ainsi un accès rapide à la documentation directement depuis l'interpréteur interactif. Utilisée sns pramètres, `help` propose aussi une aide interactive.

```py
>>> import itertools
>>> help(itertools)
>>> help(str)
>>> help(max)
>>> help('for')
>>> help()
```

Il conviendra aussi de connaitre les bibliothèques tierces, dans une moindre mesure afin de savoir trouver un bibliothèque répondant à un besoin précis. Il n'est pas nécessaire de toutes les connaitre sur le bout des doigts, ni de sortir une usine à gaz pour une petite fonctionnalité, mais simplement de **ne pas réinventer la roue**.

