---
layout: post
title: "Prog: projet p1, presentation septembre"
subtitle: ""
date: 2017-09-04
author: Sol
category: Prog
tags: divers, fr, en
finished: false
---

# Fonctionnalités 

* Dessin d'une image passée sous forme de fichier jpg ou png
* Dessin d'une image passée sous forme de liste de points 
* Dessin d'une image capturée par la webcam
* Capture directe du mouvement de la souris ce qui permet ...
* ... une reproduction d'un dessin fait par l'utilisateur en temps réel
* Export de l'image traitée en jpg ou sous forme de liste de points

## Traitement d'image
* Choix de l'algorithme de traitement de l'image:
    * a: **Vanessa**: transformations des couleurs en différentes teintes de gris, application d'un flou gaussien et detection des bords via filtre Canny.
    * b. **Janette**: algorithme non basé sur la detection de bords qui transforme les couleurs de l'image en différentes teintes de gris et tente de reproduire ces derniêres.

* Par défaut le logiciel adaptate automatique les seuils d'hyrestesis du filtre Canny en fonction d'une moyenne faite

* Un mode "expert" permet a l'utilisateur plusieurs options avancées lors tu traitement de l'image:

* Plusieurs options possibles pour le traitement de l'image avec prévisualisation:
    * Intensité du flou Gaussien
    * Valeur haute et basse de l'hyrestesis du filtre Canny
    * Transformation morphologique de l'image:
        * Erosion suivie de dilatation ce qui permet de réduire le bruit
        * Diltation suivie d'érosion ce qui permet d'amincire les traits et de boucher les trous dans certaines contours.

