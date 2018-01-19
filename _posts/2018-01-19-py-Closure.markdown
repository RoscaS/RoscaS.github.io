---
layout: post
title: "py: Closure (+JS)"
subtitle: ""
date: 2018-01-19
author: Sol
category: Py
tags: js py
finished: true
mathjax: false
---

## Code associé

* [Repo](https://github.com/RoscaS/js_tutos)


## Closure

Une closure est un espace mémoire créé quand une fonction B est définie dans une fonction A, et que B accède à des variables définies dans A.

Il n’y a qu’un espace mémoire, attaché à B, qui est réutilisé à chaque appel de B, donc si les valeurs des variables changent, B aura accès aux nouvelles valeurs à chaque appel.

Cela est utile quand on veut partager un état entre plusieurs fonctions, sans utiliser la POO, des variables globales ou modifier les paramètres qu’une fonction attend. Cela permet par ailleurs d’initialiser cet état dynamiquement.

### En Python
En Python on peut créer une fonction à la volée, et la retourner (l'objet fonction). Ca s’appelle une factory.

```py
>>> def faire_une_fonction():
...    def une_fonction_toute_fraiche():
...        return "so fresh !"
...
...    # et la c'est magic time !
...    return une_fonction_toute_fraiche 
...    # pas de parenthèse à la fin. On n’appelle pas la fonction,
...    # on retourne l'objet fonction.
 
>>> fonction = faire_une_fonction()
>>> fonction()
           'so fresh !'
```

On s'en sert principalement pour les décorateurs, mais ça permet aussi de créer du code qui contient des données préexistantes, au moment où l’on reçoit ces données.

C’est là qu’interviennent les closures.

Si à la place de retourner le message dans le return, on déffinit le message dans une variable dans la fonction `faire_une_fonction()` :

```py
>>> def faire_une_fonction():
...    fraichitude = "so fresh !"
...    def une_fonction_toute_fraiche():
...        return fraichitude
...    return une_fonction_toute_fraiche
```

Pour que ça marche, `une_fonction_toute_fraiche()` doit avoir accès à la variable définie dans la fonction du dessus afin de pouvoir l’afficher. Mais comme on retourne la fonction, **on sort de la portée de cette variable**.

Pour pallier ce problème, un espace mémoire spécial est créé automatiquement par Python qui va stocker une référence à cette valeur **dans** `une_fonction_toute_fraiche()`.

**Cet espace mémoire est appelé la closure.**

Python donne accès à cet espace mémoire :

```py
>>> fonction = faire_une_fonction()
>>> fonction
           <function __main__.une_fonction_toute_fraiche>
>>> fonction.__closure__
           (<cell at 0x7f2052fb3f18: str object at 0x7f204f2265f0>,)
>>> fonction.__closure__[0].cell_contents
           'so fresh !'
```

Cette façon de faire permet d’attacher un état à la fonction. Cet état peut être créé au niveau de la fonction factory, donc il n’a pas besoin d’être hard codé dans l’autre.

Exemple pratique:
[Sam&Max](http://sametmax.com/closure-en-python-et-javascript/)

>J’ai un sondage d’un candidat à la présidentielle. Je sauvegarde son nom et le pourcentage d’intentions de vote, puis je veux pouvoir afficher l’évolution de ces intentions de vote.
>
Puisqu’on a des états (le nom du candidat et le % d’intentions de vote), on pourrait très bien utiliser une classe et faire un objet:

```py
class Sondage:
 
    def __init__(self, candidat):
        # ici notre état est explicitement attaché à self
        self.candidat = candidat
        self.intentions_de_vote = 0
 
    def sonder(self, valeur):
        msg = "{} a {}% d'intentions de vote"
        self.intentions_de_vote += valeur
        return msg.format(self.candidat, self.intentions_de_vote)
 
>>> sondage = Sondage("Schwarzenegger")
>>> sondage.sonder(5)
           "Schwarzenegger a 5% d'intentions de vote"
>>> sondage.sonder(10)
           "Schwarzenegger a 15% d'intentions de vote"
>>> sondage.sonder(-3)
           "Schwarzenegger a 12% d'intentions de vote"
```

**En fait, les closures nous permettent d’obtenir le même effet, mais avec une fonction :**

```py
>>> def creer_sondage(candidat):
...    intentions_de_vote = 0
...
...    def sonder(valeur):
...        # ici notre état est automatiquement 
...        # stocké dans une closure
...        nonlocal intentions_de_vote 
...        intentions_de_vote  += valeur
...        msg = "{} a {}% d'intentions de vote"
...        return msg.format(candidat, intentions_de_vote)
...
...    return sonder
 
>>> sonder = creer_sondage("Schwarzenegger")
>>> sonder(5)
          'Schwarzenegger a 5% intentions de vote'
>>> sonder(10)
          'Schwarzenegger a 15% intentions de vote'
>>> sonder(-2)
          'Schwarzenegger a 13% intentions de vote'
```

Comment ce snippet marche-t-il ?

D’abord, nous avons `creer_sondage` qui est une **factory**, et qui nous fabrique puis retourne la fonction `sonder`. Mais cette dernière accède aux variables `candidat` et `intentions_de_vote` qui sont définies au-dessus. Un espace mémoire spécial est donc créé par Python pour donner accès à ces variables.

A chaque fois qu’on appelle `sonder()`, `candidat` et `intentions_de_vote` sont à leur valeur précédente, car elles sont stockées dans cet espace mémoire, et réaccédées :

```py
>>> sonder.__closure__
           (<cell at 0x7f2052fc5fa8: str object at 0x7f204f212d30>,
 <cell at 0x7f2052fb37f8: int object at 0x9f8980>)
>>> sonder.__closure__[0].cell_contents
           'Schwarzenegger'
>>> sonder.__closure__[1].cell_contents
           13
```

Ce qui explique que quand on rajoute des pourcents aux intention de vote via le passage de paramètre à chaque appel, ils se cumulent dans la variable `intentions_de_vote` : c’est toujours la même variable qui est modifiée, car elle est piégée dans la closure.

#### Python: nonlocal
Notons un mot-clé assez rarement utilisé : `nonlocal`

Pour faire simple, disons que les closures en Python sont en lectures seules, à moins qu’on précise explicitement avec `nonlocal` qu’on va utiliser une variable qui n’est pas locale et qu’on va la modifier.

C’est une contrainte spécifique à Python liée à la manière dont il gère la portée des variables, et c’est un peu relou. Bref, si vous voulez modifier la valeur d’une closure, il faut marquer la variable avec `nonlocal`.

### En JavaScript

Les closures en JavaScript marchent de la même manière qu’en Python :

```js
function creerSondage(candidat) {
    var intentions_de_vote = 0;
 
    // JS a des fonctions anonymes donc on peut 
    // la retourner cash
    return function(valeur) {
 
        // poof, toutes les variables ici 
        // sont dans une closures
        intentions_de_vote  += valeur
        return candidat + "a " +intentions_de_vote  + "% d'intentions de vote"
    };
}
 
var sonder = creerSondage("Schwarzenegger")
console.log(sonder(5))
          'Schwarzenegger a 5% intentions de vote'
console.log(sonder(10))
          'Schwarzenegger a 15% intentions de vote'
console.log(sonder(-2))
          'Schwarzenegger a 13% intentions de vote'
```

Et pas besoin de spécifier `nonlocal`.

**On utilise beaucoup plus souvent les closures en JS qu’en Python**. En Python, c’est surtout pour les décorateurs, mais en JS, comme on a des callbacks partout, il faut trouver un moyen de passer l’état du programme aux callbacks, et généralement on le fait via des closures.
