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

## IA

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