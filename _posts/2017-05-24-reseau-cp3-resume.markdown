---
layout: post
title: "Reseau: CP3 résumé (fr)"
subtitle: "résumé de la matière du CP3"
date: 2017-05-24
author: Sol
category: Reseau
tags: reseau couche4 couche7 tcp udp fr
finished: false
---

## UDP (User Datagram Protocol)

Le protocole UDP (User Datagram Protocol) est un protocole **non orienté connexion** de la couche transport du modèle TCP/IP. Ce protocole est très simple étant donné qu'il ne fournit pas de contrôle d'erreurs (il n'est pas orienté connexion...). 

* un utilisteur d'UDP doit compter avec les événements suivants:
    * les données peuvent être perdues
    * les mêmes données peuvent arriver plusieurs fois
    * l'ordre des données peut être modifié
    * le déstinataire peut être surchargé par les données de l'émetteur
    * les données ne peuvent dépasser le MTU


## TCP (Transmission Transfer Protocol)

* protocol **sûr**, qui utilise les principes suivants par défaut:
    * protocole a fenètre (de type `GO-BACK N`)
    * implicit retransmission
* les messages sont appelés _segments_
* on utilise uniquement des confirmations positives (`ACK`, qui peuvent être >groupés) t un timer (minuteir) est calculé pour chaque segment
* TCP offre un transport sûr (fiable) des données
    * les données sont transmises exactement une fois (pas de perte, pas de duplication)
    * pas de modification de l'ordre
    * contrôle de flux


## 3 way handshake

Établissement de la connexion entre un serveur et un client en 3 temps:
* client envoie un `syn` (Wesh, je peux me co sur toi?)
* serveur renvoie un `syn`, `akc` (Acknowledgment) (Ouais gros, fait ton kiff)
* client renvoie un `akc` pour confirmer qu'il a bien reçu la réponse (t'es trop cool man thanks)

Une fois cet connexion établie l'envoie de données du serveur peut commancer.

## fin de connexion

Une fois les données transférées, TCP met fin courtoisement à la connexion par un autre échange du même genre que le 3 way handshake:

* une fois que le client à reçu tout ce qu'il voulait, il envoie un `fin` 
* le serveur répond par un `fin` `ack`
* le client renvoie un `ack`

## Protocole à fenêtre (TCP)

Limitation du nombre d'`ack` du serveur pour désengorger le réseau. On fixe un nombre de séquences au bout duquel un accusé de réception est nécessaire. Ce nombre est stocké dans le champ **window** de l'en-tête `TCP`

La taille de cette fenêtre n'est pas fixe. **Le serveur** peut inclure dans ses `akc` dans le champ `window` une taille de fenêtre plus adaptée pour x ou y raisons.

* Lorsque l'`ack` du serveur demnde un augmentation de la fenêtre, le client déplace le bord droit de la fenêtre

![](http://static.commentcamarche.net/www.commentcamarche.net/pictures/internet-images-plus.gif)

* Dans le cas d'une diminution, le client attend que le bord gauche avance (avec l'arrivée des `ack`)

![](http://static.commentcamarche.net/www.commentcamarche.net/pictures/internet-images-moins.gif)

Cette notion est appelée **stream tcp**.

## Stream TCP

* l’unité de gestion de la fenêtre n’est pas le message mais l’octet
* l’application lit les données comme elles viennent
* la grandeur de la fenêtre est variable!
    * le destinataire gère dynamiquement la taille de la fenêtre chez l’émetteur
    * chaque confirmation contient une indication de fenêtre
* la position dans le flux de données est indiquée par les numéros de séquence (pointeurs)

## Le timer

* TCP travaille en implicit retransmission (confirmation positives seulement)
* les temps de transfert peuvent varier énormément (LAN, WAN)
* timer trop petit: retransmissions inutiles
* timer trop grand: retard dans la découverte des problèmes (attention: GO-BACK N)
* TCP mesure en permanence les temps de réponse et adapte le timer
* une moyenne glissante et une variance glissante sont calculées et utilisées pour le calcul du time