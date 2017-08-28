---
layout: post
title: "Divers: Kerbal Space Program (en-fr)"
subtitle: ""
date: 2017-06-27
author: Sol
category: Divers
tags: divers, fr, en
finished: false
---

# Préambule

## Friction

> Si une voiture roule à 120km/h et que nous la mettons au point mort et entamons un virrage, après un certain temps cette voiture se retrouverait à l'arrêt. Notre intuition nous pousse à accélérer pour compenser cette perte de vitesse.

La raison pour laquelle on pense est que toutes nos expériences sur terre implique la **friction** (frottement). Dans l'exemple précédent, il y a un frottement avec l'air sur la carosserie de la voiture ainsi que le contact des pneuds avec la route.

La **friction** peut être vue comme une **force qui tire** alors que la pédale d'**accélération** nous **pousse**. Si nous coupons le moteur, la friction continue à nous tirer ce pourquoi nous perdons de la vitesse, jusqu'à l'arrêt.

Dans l'espace, il n'y a pas de friction, un corp en déplacement se déplace tant que quelque chose ne l'arrête pas.

> Une des loi de Newton spécifie qu'un objet en déplacement ou au repos le reste à moins qu'une force ne s'applique sur eux.

Une force peut être vue comme une poussée (dans un sens ou dans un autre).

## Velocité, vitesse, direction et accélération

Quand on parle de vélocité, on parle de la **vitesse** à laquelle un objet se déplace ainsi que la **direction** dans laquelle il le fait.

* Si la vitesse d'un objet est stable et qu'il tourne, cet objet subit un changement de vélocité.
> Si nous sommes dans une voiture et que nous effectuons un virage à 90 degrés, nous devons accélérer pour compenser la perte de vitesse

**L'accélération n'est rien d'autre qu'un changment de vélocité.** En physique quand quelque chose accélère, c'est soit que sa **vitesse change** soit que sa **direction change**, soit **les deux**.

### Complément d'informations
La relation mathématique entre **position**, **vélocité** et **accélération** et leur exploration est ce qui à donné naissance à la branche des maths appellée annalyse.



![alt](https://wiki.kerbalspaceprogram.com/images/thumb/c/cd/Simple_orbit_diagram.svg/900px-Simple_orbit_diagram.svg.pngk)







# Orbite

[coeleve](https://coeleveld.com/kerbal-space-program-how-to-get-into-orbit-and-back/)

## Notions

* Prograde $$ \Rightarrow $$ avant
* Retrograde $$ \Rightarrow $$ arrière
* Radial $$ \Rightarrow $$ vers le centre de la sphère
* Anti-radial $$ \Rightarrow $$ vers l'extérieur de la sphère
* Normal $$ \Rightarrow $$ monter (inclinaison positive)
* Anti-normal $$ \Rightarrow $$ descendre (inclinaison négative)

### Gravity turn

* Sortie dans l'espace: tout droit pendant 70km. 
* Mise en orbite: implique de commencer son ascension avec une trajectoire en arc de cercle en tentant de gagner suffisament de**vitesse verticale** et de**vitesse latérale** pour contrer la force de pesanteur.

Une mise en orbite dans le sens de rotation de la planete (est pour Kerbin) permet d'ajouter la vitesse de rotation planétaire à notre vitesse orbitale.

Pour un gravity turn réussit, il faut faire en sorte de toujours garder l'indicateur de niveau de la Navball proche du marqueur prograde en incrémentant doucement vers les 90 degrés.

![alt](https://coeleveld.com/wp-content/uploads/2016/09/Kerbal_Navball_Gravity_Turn.png)

* Marqueur jaune prograde $$ \Rightarrow $$ vecteur accélération
* Le marqueur orange $$ \Rightarrow $$ vers où pointe la fusée
* ..., 45, 90, 135, ... $$ \Rightarrow $$ compas
* les autres traits $$ \Rightarrow $$ inclinaison

### Vitesse terminale

On saute d'un avion. Dans un premier temps notre chute accélère mais de moins en moins plus le frotement de l'air s'intensifie (à cause de l'accélération). À un moment donné, nous n'accélérons plus et notre vitesse est constante. C'est ce qui s'appelle la vitesse terminale. **Le moment où le frotement de l'air égalise la force de pesanteur.**

Dans le cas d'une fusée, à la place de la gravité c'est la poussée et elle va de bas en haut mais le principe est le même. Pour une ascension éfficace, **il ne faut pas qu'elle dépasse la vitesse terminale** sous peine de générer des turbulences et de la chaleur.

| m | m/s |
| 500 | 105 |
| 1000 | 110 |
| 2000 | 120 |
| 3000 | 130 |
| 5000 | 160 |
| 6000 | 180 |
| 7000 | 200 |
| 8000 | 220 |
| 10 000 | 260 $$ \Rightarrow $$ gravity turn|

<span style="color:red"> Garder en tête que si on subit des turbulences où que les pièces s'échauffent **il faut baisser la poussée!** </span> 

### Orbite

* **Apoapsis(AP):** Point le plus haut dans notre orbite.
* **Periapsis(PE):** Poinr le plus bas de notre orbite.

#### Circularizing a parabolic orbit
Le point le plus efficace pour augmenter l'hauteur de la Periapsis et l'Apoapsis. C'est là qu'on donne de la poussée en pointant **Prograde** pour lever la Periapsis jusque à ce qu'elle soit entre 70 et 80km. Plus la poussée nécéssaire est longue plus il faut anticiper le moment où nous allons arriver à l'Apoapsis.


### Entrée
Poussée **Retrograde** (à l'Apoapsis pour une efficacité optimale) jusqu'à ce que la Periapsis soit à 20km (ou moins).

## En détails

### 0m - 1000m décolage

* Poussée maximum `Z`
* SAS on `T`
* Décolage
* **Atteindre 100m/s à 1000m**

### 1000m - 10 000m Vitesse terminale & initiation du gravity turn

* Incliner de 10 degrés vers l'est
    * SAS off si il faut pour aider
* Faire attention à ne pas dépasser les vitesses terminales

### 10 000m - 18 000m Basse atmosphère

> S'attendre à des turbulences et de la chaleur vers les 500 m/s. C'est causé par la vitesse et non pas l'altitude. Ici l'atmosphère est plus fine et ce n'est pas un problème.

* Poussée maximum
* Incliner de 45 degrés vers l'est
* Découplage si nécéssaire

### 18 000m - 69 000m Partie haute de l'atmosphère

> À partir d'ici il y a peut de choses qu'on puisse faire pour changer de trajectoire et toute tentative sera très peu rentable

* Inclinaison de 90 degrés vers l'est
    * SAS off si il faut pour aider mais le minimum possible
* Découplage si nécéssaire
* On coupe les moteurs à 70km

### 70 000m Espace autour de Kerbin
* Rejoindre l'Apoapsis
* Pleine poussée pour lever la Periapsis (longue poussée, anticiper avant point AP)
* On coupe les moteurs quand la Periapsis est à 70km

### 70 000m 0m Entrée

* Rejoindre l'Apoapsis
* Pleine poussée Retrograde
* On coupe une fois la Periapsis sous les 20km
* Découplage de ce qu'il reste
* Orientation Retrograde des boucliers thermiques
* Si la chaleur change en turbulences et que la vitesse tombe sous 500m/s c'est réussit
* Déploiement des parachutes à 250m/s







## Best mods

[Galileo + mods](https://www.youtube.com/watch?v=D5iYj8cc3Ko&t=695s)


## Moteurs

![alt](/00illustrations/ksp/engines1.png)

![alt](/00illustrations/ksp/tweakingEngine.png)



## Mise en Orbite

![alt](/00illustrations/ksp/orbitRocket.png)

## Divers

![alt](/00illustrations/ksp/align.png)

