---
layout: post
title: "Algo: Recursivité (fr)"
subtitle: "Récursivité"
date: 2017-06-02
author: Sol
category: Algo
tags: algo recursivite fr
finished: false
---

# Récursivité

## Sources
[visualgo.net](https://visualgo.net/en/recursion)  
[u-picardie](https://www.u-picardie.fr/~furst/docs/3-Recursivite.pdf)  _aller fouiller un peu, ça semble bien !_  
[wikipedia.org](https://fr.wikipedia.org)  
[stackoverflow](https://stackoverflow.com)

## Codes
[Repo](https://github.com/RoscaS/algo_sort)

## Definition

1. Un objet est dit récursif si il se définit à partir de lui-même, _si il apparait dans sa définition_.
2. Une construction est **récursive** si elle se définit à partir d'elle-même.

 ><img src="/00illustrations/algo-recur/sier.png" height="auto">
 >
 >_triangle de Sierpinski_

Quelques acronymes récursifs courants:  

    PHP, GNU, LAME, WINE, YAML

## En informatique

En informatique, un programme est dit **récursif** s'il s'appelle lui même. Il s'agit donc **forcément d'une fonction**.

_Exemple:_ $$ \large factorielle $$ $$ \large n! = 1 \cdot 2 \cdot \ldots \cdot n \Rightarrow n! = n \cdot (n-1)! $$

<img src="/00illustrations/algo-recur/facto1-def.png" height="250">

- L'appel récursif est traité comme n'importe quel appel de fonction.

<img src="/00illustrations/algo-recur/facto1.png" height="700">

## Condition d'arrêt

- Pusqu'une fonction récursive s'appelle elle-même, il est impératif qu'on prévoit une <span style="color:red"> condition d'arrêt </span> à la récursion, sinon le programme ne s'arrête jamais !

- **On doit toujours tester en premier la condition d'arrêt**, et ensuite, si la condition n'est pas vérifiée, lancer un appel récursif.

_Exemple de la factorielle:_ <span style="color:red"> $$ \large si $$ </span> $$ \large n \neq 1, \quad n \cdot (n-1)! $$ <span style="color:red"> $$ \large sinon$$</span>$$ \large, \quad n! =1 $$

<img src="/00illustrations/algo-recur/facto2-def.png" height="250">

> **main()**  

<img src="/00illustrations/algo-recur/facto2.png" height="600">


<br>[animation](https://visualgo.net/en/recursion)   

## Pile d'execution (stack)

La récursivité fonctionne car chaque appel de fonction est différent.
L'appel d'une fonction se fait dans un  <span style="color:red"> contexte d'execution </span> propre, qui contient:

* l'adresse mémoire de l'instruction qui a appelé la fonction
* les valeurs des paramètres et des variables définies par la fonction  

<br>
<!--<iframe width="800" height="550" frameborder="0" src="http://pythontutor.com/iframe-embed.html#code=def%20factorielle%28n%29%3A%0A%20%20%20%20if%20%28n%20%3D%3D%201%29%3A%0A%20%20%20%20%20%20%20%20return%201%0A%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20return%20n%20*%20factorielle%28n%20-%201%29%0A%0A%0Aprint%28factorielle%284%29%29&codeDivHeight=400&codeDivWidth=350&cumulative=false&curInstr=12&heapPrimitives=false&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe>-->

<!--<div class="image" style="width: 100%">
    <a  href="https://visualgo.net/en/recursion"><img src="/00illustrations/algo-recur/fibo8.gif" ></a>
</div>-->

[version interactive](https://goo.gl/QGxNHe)
<img src="/00illustrations/algo-recur/facto-py.gif" height="auto">


Prévoir à l'avance le nombre d'appels d'une fonction récursive pouvant être en cours simultanément en mémoire est **impossible**. La récursivité suppose donc une <span style="color:red"> allocation dynamique </span> de la mémoire (à l'execution).

**Attention:** éxecuter trop d'appels de fonction fera déborder le stack !

## Récursif VS itératif

Il est souvent possible d'écrire un même algorithme en itératif et en récursif.

L'exécution d'une version récursive d'un algorithme est généralement un peu moins rapide que celle de la version itérative, même si le nombre d'instructions est le même (à cause de la gestion des appels de fonction).

<!--![/00illustrations/algo-recur/recVSit.png](/00illustrations/algo-recur/recVSit.png)-->

<div class="image" style="width: 100%">
    <a  href="/00illustrations/algo-recur/recVSit.png"><img src="/00illustrations/algo-recur/recVSit.png" ></a>
</div>

Un algorithme récursif peut conduire à exécuter bien plus d'instructions que la version itérative.

_Exemple:_ la suite de Fibonacci 

### Suite de Fibbonacci ($$ \large n = 45 $$)

> Par définition, les deux premiers nombres de la suite de Fibonacci sont 1 et 1 [ou](https://fr.wikipedia.org/wiki/Suite_de_Fibonacci) 0 et 1. Nous devons en tenir compte pour l'implémentation. 

#### récursif:

```c++
int fib1(int n) {
    if (n == 0)
    {
        return 0;
    }
    else if (n == 1)
    {
        return 1;
    }
    return fib1(n-1) + fib1(n-2);
}

// output:
// 1134903170
// 10.859 seconds
```

Temps d'execution: $$ \large \color{red}{10.859} \; secondes $$

_version plus compacte:_

```c++
int fib2(int n) 
{
    if (n < 2)
    {
     return n;
    }     
    return (fib2 (n - 1) + fib2 (n - 2));
}

// output:
// 1134903170
// 10.738 seconds
```

Temps d'execution: $$ \large \color{red}{10.738} \; secondes $$

_version très compacte:_

```c++
 int fib3(int n) { return (n < 2)? n: fib3(n-1)+fib3(n-2); }

// output:
// 1134903170
// 9.752 seconds
```

Temps d'execution: $$ \large \color{red}{9.752} \; secondes $$

#### itératif:

```c++
int fib_it(int n)
{
    int a = 1; 
    int b = 1;
    for (int i = 3; i <= n; i++) {
        int c = a + b;
        a = b;
        b = c;
    }           
    return b;
}

// output:
// 1134903170
// 0.389 seconds
```

Temps d'execution: $$ \large \color{green}{0.389} \; secondes $$


Les différences de temps d'éxecution parlent pour elles mêmes et cette animation nous aide à en comprendre la raison:

<div class="image" style="width: 100%">
    <a  href="https://visualgo.net/en/recursion"><img src="/00illustrations/algo-recur/fibo8.gif" ></a>
</div>

## Récursivité terminale et non terminale

En programmation, on distingue, pour des raisons d'efficacité, 2 types d'algorithmes récursifs.

### 1. récursivité Terminale

Un algorithme est terminal si aucune opération ne sui l'appel récursif.
C'est l'appel récursif qui "termine" l'algorithme.
Toutes les opération sont faites avant l'appel récursif: La phase de remontée devient alors inutile (elle ne fait aucun traitement, hormis le réajustement de la pile).
La récursivité terminale revient à appliquer l'adage *Diviser pour régner* de la façon suivante:

* On traite la données courante
* Le même traitement est appliqué au reste des données

### 2. Récursivité non- terminale

Un algorithme récursif est **non-terminal** lorsque des opérations suivent l'appel récursif. C'est notament le cas lorsque l'appel récursif est utilisé dans une expression pour calculer un résultat. Toutes les opérations ne sont pas faites avant l'appel récursif: la phase de remontée fait une partie du traitement, elle contient souvent la majorité des opérations. La récursivité no-terminale revient à appliquer l'adage *diviser pour régner* de la façon suivante:

* On réduit le problème à son cas trivial
* On traite le cas et on élargit l'ensemble des données traitées.

## Points cruciaux pour la programmation récursive

* Avoir une condition terminale
* Avoir un appel récursif dont un des paramètres converge vers la condition terminale
* S'assurer que la condition est **TOUJOURS** atteinte après un nombre fini d'appels

## Quand éviter d'utiliser un algorithme récursif ?

* Lorsque la récurence est d'ordre plus grande que 1 (c'est à dire que l valeur au rang $$ \large n $$ ne dépend pas seulement du rang $$ \large n-1 $$, mais aussi de $$ \large n-2 $$, voir $$ \large n-3 $$,... ) 

* Une utilisation "aveugle" de la récursivité impliquera une redondance de calculs. Dans ce cas, il peut être utile de dérouler l'algorithme avant de l'implémenter pour s'assurer qu'il ne génère pas d'opeerations inutiles.

## Récursivité et itérations 

Tout algorithme récursif a un équivalent itératif. La réciproque est également vraie en théorie, mais le passage de l'un à l'autre n'est pas toujours aisé. La procédure de **derécursivation** consiste à gérer dans le programme le comportement de la pile lors des appels récursifs. 

En règle générale, un algorithme récursif est moins performant qu'un algorithme itératif : Chaque appel récursif nécessite d'empiler le contexte de la fonction (cadre de pile), puis la condition terminale atteinte, dépiler ce contexte avant d'exécuter les instructions qui suivent l'appel récursif.  La création, le changement et la libération de contextes sont donc des opérations supplémentaires non réalisées par l'équivalent itératif. **Les algorithmes récursifs sont donc moins performants que leurs équivalents itératifs**. 
 
Exception à cette règle, la récursivité terminale est détectée par la majorité des compilateurs et comme il n'y pas d'instruction à exécuter après l'appel récursif terminal, la phase de remontée pourra être supprimée. **Dans la plupart des cas, une fonction récursive terminale aura donc la même performance que son équivalente itérative**. La récursivité sera alors privilégiée, pour sa lisibilité et son élégance.


## Précisions sur les deux types de récursivité (terminal/non terminal)
 
* Une fonction récursive est dite <span style="color:red">terminale</span> si aucun traitement n'est effectué à la remontée d' un appel récursif (sauf le retour d'une valeur).

* Une fonction récursive est dite <span style="color:green">non terminale</span> si le résultat de l'appel récursif est utilisé pour réaliser un traitement (en plus du retour d'une valeur).

 Exemple de non terminalité : forme récursive non terminale  de la factorielle, les calculs se font à la remontée. C'est l'exemple au détbut de cette page !

 ```c
fonction avec retour entier factorielleNT(entier n)
debut
    si (n = 1) alors
        retourne 1;
    sinon 
        retourne n*factorielleNT(n-1);
    finsi
fin
 ```

### Exemple de terminalité : 

 On peut facilement rendre notre fonction factorielle terminale:

 ```c
// la fonction doit être appelée en mettant resultat à 1

 fonction avec retour entier factorielleT(entier n, entier resultat)
 debut
    si (n =1) alors
        retourne resultat;
    sinon
        retourne factorielleT(n-1, n * resultat);
    finsi
fin
```

### Intérêt de la récursivité terminale

Une fonction récursive <span style="color:red">terminale</span> est en théorie plus efficace (mais souvent moins facile à écrire) que son équivalent non terminale: il n'y a qu'une phase de descente et pas de phase de remontée.

En récursivité <span style="color:green">terminale</span> , les appels récursifs n'ont pas besoin d'êtres empilés dans la pile d'exécution car l'appel suivant remplace simplement l'appel précédent dans le contexte d'exécution. 

Certains langages utilisent cette propriété pour exécuter les récursions terminales aussi efficacement que les itérations.

Il est possible de transformer de façon simple une fonction récursive terminale en une fonction itérative : c'est la <span style="color:green">dérécursivation</span>.


#### Une fonction récursive terminale a pour forme générale:

```c
fonction avec retour T recursive(P)
debut
    I0
    si (C) alors
        I1
    sinon
        I2
        recursive(f(P));
    finsi
fin    
```
* **T** est le type de retour
* **P** est la liste des paramètres
* **C** est la condition d'arrêt
* **I0** le bloc d'instructions exécuté dans tous les cas
* **I1** le bloc d'instructions exécuté si **C** est vraie
* **I2** est le bloc d'instructions exécuté si **C** est fausse
* **f** la fonction de transformation des paramètres

#### Fonction itérative correspondante:

```c
fonction avec retour T iterative(P)
debut
    IO
    tantque (non C) faire
        I2
        P <- f(p);
        I0;
    fintantque
    I1
```

### Dérécursivation de la factorielle terminale:

```c
// cette fonction doit être appelée avec a=1
fonction avec retour entier factorielleRecurTerm(entier n, entier a)
debut
    si (n <= 1) alors
        retourne a;
    sinon
        retourne factorielle(n-1,n*a);
    finsi
fin
```

```c
fonction avec retour entier factorielleIter(entier n, entier a)
debut
    tantque (n > 1) faire
        a <– n*a;
        n <– n-1;
    fintantque
    retourne a;
fin
```

#### Forme générale d'une fonction récursive non terminale:

```c
fonction avec retour T recursive(P)
debut
    I0
        si (C) alors
            I1
        sinon
            I2 
            recursive(f(P));
            I3
    finsi
fin
```

**T** est le type de retour
**P** est la liste des paramètres
**C** est la condition d'arrêt
**I0** le bloc d'instructions exécuté dans tous les cas
**I1** le bloc d'instructions exécuté si **C** est vraie 
**I2** et I3 les blocs d'instructions exécutés si **C** est fausse
**f** la fonction de tranformation des paramètres


La fonction  itérative correspondante  doit  gérer  la  sauvegarde  des  contextes d'exécution (valeurs des paramètres de la fonction). 

**La  fonction  itérative  correspondante  est  donc  moins  efficace  qu'une  fonction écrite directement en itératif.**

## Algorithmes de tri en mode récursif

_à compléter_

## Résumé

### Récursivité

Un objet est dit récursif s'il apparait dans sa définition.
* structure de données récursives: _listes_, _arbres_ et _graphes_
* algorithmes récursif: si une des opérations est un appel à l'algorithme en **lui même** mais à un rang inférieur

> la récursivité c'est quand le sous-problème est de la **même nature** que le problème initial mais à un rang inférieur 

Chaque appel de la fonction récursive est une nouvelle **instance de la fonction** ($$ \neq $$ même fonction car les paramètres sont différents).

* nouvelle instance à chaque appel
* chaque appel est différent du précédent, il n'y a pas _duplication_ du contexte, c'est un contexte différent à chaque appel.
* parmètres différents $$ \Rightarrow $$ fonction différente

Dire  _"une fonction récursive est une fonction qui se rappelle elle-même"_ est donc <span style="color:red"> **FAUX**</span>

### Phases d'un algorithme récursif

L'éxécution proprement dite peut se décomposer en deux temp:

1. **Phase de descente**: appels récursifs (et création d'un contexte pour chacun d'entre eux) jusqu'à la condition terminale

2. **Phase de remontée**: retour des résultats et libération des contextes devenus inutiles

### Terminal & non-terminal

* **Terminal**: aucune opération ne suit l'appel récursif  
$$ \quad \Rightarrow $$ la phase de remontée est inutile, elle ne fait aucun traitement, hormis le réajustement de la pile.

* **Non-terminal**: des opérations suivent l'appel récursif. (On a besoin du résultat du rang inférieur avant de pouvoir traiter le problème dans l'instance courante et renvoyer le résultat).  
$$ \quad \Rightarrow $$ la phase de remontée fait une partie du traitement, elle contient des(/la majorité) des opérations.

La question à se poser et qui ne nécéssite pas de comprendre l'algorithme c'est:

>**Est-ce qu'un return précède un appel de la fonction-elle même ?**
>* oui $$ \Rightarrow $$ récursivité ** non-terminale**
>* non $$ \Rightarrow $$ récursivité ** terminale**


