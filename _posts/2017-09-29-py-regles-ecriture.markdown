---
layout: post
title: "Py: Regles d'écriture "
subtitle: "Pour un code Pythonique"
date: 2017-09-29
author: Sol
category: py
tags: tools
finished: false
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