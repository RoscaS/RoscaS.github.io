---
layout: post
title: "Algo: Résumé CP2"
subtitle: "Algorithmes de tri et récursivitee"
date: 2017-06-13
author: Sol
category: Algo
tags: algo tri recursivite
finished: false
---
## Liens:
* Internes:  
[algorithmes de tri simples](/algo/Algo-tri.html)  
[récursivité](/algo/recursivite.html)  
[repo(privé)](https://github.com/RoscaS/algo_sort)  

* Externes:  
[VisuAlgo](https://visualgo.net/en)  

## 1. Tri

### Tri Bulle
$$ O(n^2) $$
* stable
* **Sait détecter si un tableau est trié** (amélioration flag)
* Fait remonter les petites valeurs en **échangeant** 2 valeurs contigues.
* À chaque parcours l'élément avec la clé la plus petite pas encore trié se retrouve en position définitive.
* À chaque parcours les autres éléments se déplacent vers leurs position définitive d'une case.

### Tri Extract
$$ O(n^2) $$
* pas stable
* **Innéfficace pour les tableau déjà trié**
* Parcours le tableau et **sélectionne** l'élément avec la clé la plus faible
* Echange à la fin du parcours avec l'élément à l'indice "gauche"
* Incrémentation de "gauche"


### Tri Insert
$$ O(n^2) $$
* stable
* **Utile pour insérer une valeur dans un tableau déjà trié ou partiellement trié**
* Pour une valeur à **insérer** dans un tableau déjà trié:
    * on décale chaque élément du tableau vers la droite tant que les clés sont plus grandes que la valeur à insérer
    * on insère la valeur une fois que la clé de l'élément  à sa gauche est plus grande

### Tri par Base
$$ O(n) $$
* stable
* **Efficace en temps mais pas en mémoire**
* Tri basé sur la **distribution**
* Le nombre de files nécéssaire dépend de la base. (10 en base 10, 26 pour des char, 16 en hexa,...) 
* Autant de distributions que de caractères qui composent l'élément avec la clé la plus **longue** du tableau à trier.
* Les distributions commencent par le caractère le plus à droite, du premier élément du tableau
* Placement de cet élément dans la file correspondant à ce caractère et ainsi de suite pour tous les éléments
* Celon le principe du **FIFO** on sort les éléments des files dans l'**ordre croissant** des files (pour un tri croissant) et on les replace dans le tableau initial.
* On répète pour le caractère à l'indice -1,-2,...

#### fonction nombreDeParcours

Nombre de parcours =  **nombre de chiffres qui compose un nombre**.
l'entier renvoyé par un cast `entier` du log en base 10 d'un nombre sera égal au nombre de chiffre qui le compose -1. Nous ajoutons donc +1.

```c++
int nbParcours(int *t, int g, int d) {
    return log10(maximum(t,g,d))+1;
}
```
_Le cast est fait automatiquement par le type de la valeur de retour_

#### fonction extraireUnite

**retourner une unité spécifique d'un nombre** 

```c++
int extraireUnite(int nb, int pos) {
    return (static_cast<int>(nb/pow(10,pos))%10);
}
```
Ici, le cast est nécessaire pour éviter une erreur de compilation


### Comparaison

| Algorithme | Complexité au pire | Stabilité | Famille* | Remarques
| --- | --- | --- | --- | --- |
| Bulles et améliorations | $$ O(n^2) $$  | oui | Echange | Sait détecter un tableau trié | 
| Extraction | $$ O(n^2) $$ | non | Sélection | Inefficace sur un tableau déja trié (le pire) | 
| Insertion | $$ O(n^2) $$ | oui | Insertion | Intéressant pour insérer des vlaeurs dans un tableau déjà trié*  | 
| Base | $$ O(n) $$ | oui | Distribution | Intéressant pour les petites valeurs ("petit" nombre de chiffres) | 

<br>

> * On peut trier les valeurs au fur et à mesure. Pour les autres, il est nécéssaire de les avoir toutes avant de commencer. En  _O(n)_  dans le meilleur cas.

## 2. Récursivité

Un objet est dit récursif s'il apparait dans sa définition.
* structure de données récursives: _listes_, _arbres_ et _graphes_
* algorithmes récursif: si une des opérations est un appel à l'algorithme en **lui même** mais à un rang inférieur

> la récursivité c'est quand le sous-problème est de la **même nature** que le problème initial mais à un rang inférieur 

Chaque appel de la fonction récursive est une nouvelle **instance de la fonction** ($$ \neq $$ même fonction car les paramètres sont différents).

* nouvelle instance à chaque appel
* chaque appel est différent du précédent, il n'y a pas _duplication_ du contexte, c'est un contexte différent à chaque appel.
* paramètres différents $$ \Rightarrow $$ fonction différente

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

> <span style="color:green"> **Est-ce qu'un return précède un appel de la fonction-elle même ?** </span>
>* oui $$ \Rightarrow $$ récursivité ** non-terminale**
>* non $$ \Rightarrow $$ récursivité ** terminale**

### Pour la programmation récursive il faut...
* avoir une condition terminale
* avoir un appel récursif dont un des paramètres converge vers la condition terminale
* s'assurer que la condition est toujours atteinte après un nombre fini d'appels

### Eviter la récursivité quand...
* la récurence est d'ordre plus grand que 1 (c'est à dire que la valeur au rang $$ n $$ ne dépend pas seulement du rang $$n-1$$ mais aussi de $$n-2$$, voir $$n-3$$, ...)


### Récursivité vs itération

La récursivité est toujours plus lente que l'itération sauf pour la récursivité terminale. Cependant, la majorité des compilateurs détectent et supprime la phase de remonté. Une fonction récursive terminale est donc aussi rapide qu'une fonction itérative.  

$$\Rightarrow $$ De part sa lisibilité accrue, la **récursivité terminale** est à privilégier sur l'itération.



nombre: 2 5 3 4 8
idx:        0 1 2 3 4
mot:       p o u l e

première passe : dernier caractère (8 et e)
ensuite, caractère à l'indice -1 (4 et l)
...