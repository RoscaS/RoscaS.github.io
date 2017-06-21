---
layout: post
title: "Divers: Définitions (en-fr)"
subtitle: "définitions diverses"
date: 2017-05-26
author: Sol
category: Divers
tags: divers, fr, en
finished: false
---
# Définitions


## Divers

### Philanthropie 
(du grec ancien φίλος / phílos « amoureux » et ἄνθρωπος / ánthrôpos « homme », « genre humain ») **est la philosophie ou doctrine de vie qui met l'humanité au premier plan de ses priorités**. Un philanthrope cherche à améliorer le sort de ses semblables par de multiples moyens.

## IA (types)

### Sources:
[oezratty.net](http://www.oezratty.net/wordpress/2016/avancees-intelligence-artificielle-1/) (fr) impressionnant !

### intelligence symbolique 
Résout les problèmes avec de la connaissance (c’est le domaine actuel de l’IA et du machine learning).


### intelligence computationnelle
Résout des problèmes avec des données issues d’exemples et de l’apprentissage. Ce dernier domaine intègre notamment les réseaux neuronaux, la logique floue et les algorithmes génétiques.


### le symbolisme
Se focalise sur la pensée abstraite, 

### le connectionisme  
se focalise sur la perception, dont la vision, la reconnaissance des formes et qui s’appuie notamment sur les réseaux neuronaux artificiels 

### le comportementalisme 
s’intéresse aux pensées subjectives de la perception. C’est dans ce  domaine que l’on peut intégrer: 
    * l’informatique affective (affective computing) qui étudie les moyens de reconnaitre, exprimer, synthétiser et modéliser les émotions humaines. C’est une capacité  qu’IBM Watson est censé apporter au robot Pepper d’Aldebaran / Softbank.

> Automatisation des processus cognitifs et en s’appuyant sur quatre étapes : l’observation des faits et données, leur interprétation, leur évaluation et la décision, avec une action ou une proposition d’actions, souvent basée sur des statistiques.

## IT

### idiom

An "idiom" in (non-programming) language is a saying or expression which is unique to a particular language. Generally something which doesn't follow the "rules" of the langauge, and just exist because native speakers "just know" what it means. 

Moving this to the programming arena, we get things like:

```c
if(c=GetValue())
    {...}
 ```
which actually means:

 ```C
c = GetValue();
if (c != 0)
    {....}
 ```

which every C/C++ programmer understand, but would totally baffle someone coming from a different programming language.

### endiannes

> Ordre dans lequel les octets sont organisés dans une case mémoire **ou dans une communication**. Big endian et Little endian sont deux architectures différentes.

#### Big endian
**byte de poids fort** à gauche.  
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:| 0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| A0 | B7 | 07 | 08 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| A0 | B7 | | 07 | 08 | ... |


<span style="color:#F92672">**Tous les protocoles TCP/IP communiquent en big-endian**</span>


#### Little endian
**byte de poid faible** à gauche.   
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:|  0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| 08 | 07 | B7 | A0 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 07 | 08 | | A0 | B7 | ... |

<span style="color:#F92672">**X86 fonctionne en Little endian**</span>


## Algo

#### TAD 
Spécification mathématique d’un ensemble de valeurs et de l’ensemble des opérations qu’elles peuvent effectuer. Il est qualifié d’abstrait car il correspond à un cahier des charges qu’une structure de données doit ensuite implémenter.

#### SCD
Implémentation concrète d’un TAD dans un langage.

#### Heuristique 
Méthode qui fournit rapidement (temps polynomial) une solution réalisable mais pas forcément optimale pour un problème d’une complexité hors du possible pour l’approche statistique.

#### Déterministe
Toute execution de cet algorithme sur les mêmes données donne lieu à la même suite d’opérations.

#### Linéaire
il existe un premier et un dernier élément, tous les autres éléments ont un élément qui les précède et un autre qui les suit.

#### Homogène 
Même type

#### accès séquentiel
On accède aux données dans un ordre défini contrairement à un accès direct où on peut accéder à n’importe quel élément quand on veut.

#### Dichotomique

On peut illustrer l'intérêt de la recherche dichotomique par l'exemple du jeu suivant.

A et B jouent au jeu suivant :

A choisit un nombre entre 0 et 100, et ne le communique pas à B, B doit trouver
ce nombre en posant des questions à A dont les réponses ne peuvent être que oui ou non. 

B doit essayer de poser le moins de questions possible. Une stratégie pour B est d'essayer tous les nombres, mais il peut aller plus rapidement comme le montre le scenario suivant :

A choisit 66 et attend les questions de B :
* B sait que le nombre est entre 0 et 100 

Au milieu se trouve 50, B demande donc :  
_Est-ce que le nombre est plus grand que 50?_
A répond _Oui_ .

* B sait maintenant que le nombre est entre 51 et 100, au milieu se trouve 75, il demande donc :

_Est-ce que le nombre est plus grand que 75?_
A répond  _Non_

* Et ainsi de suite : 

 _Plus grand que 63?_ ($$ 63 = \large \frac{51+75}{2}$$)   
 _Oui_     
 _Plus grand que 69?_ ($$ 69 = \large \frac{63+75}{2}$$)     
 _Non_     
 _Plus grand que 66?_ ($$ 66 = \large \frac{69+63}{2}$$)     
 _Non_     
 _Plus grand que 65?_ ($$ 65 \large \approx \frac{63+66}{2}$$)      
 _Oui_     

* B sait maintenant que le nombre est entre 66 et 66, autrement dit qu'il s'agit de 66 : il a trouvé le nombre choisi par A en seulement 7 questions.