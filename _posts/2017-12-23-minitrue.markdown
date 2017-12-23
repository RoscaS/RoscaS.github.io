---
layout: post
permalink: /minitrue/
date: 2017-12-23
finished: false
mathjax: true
---

# Minitrue

## Links

* [doc original](https://docs.google.com/document/d/1AcVB2a68YHTU2iRsqCZD25fYDofjoib9ijSqoMVE-Do/edit?usp=sharing)

## Abstract

Minitrue est un réseau social privé regroupant des artistes, des penseurs, des philosophes et journalistes. Les membres publient, votent et échangent sur les créations entre eux en interne. Une selection des oeuvres les plus populaires peuple la partie publique du site. Un jeu d'esprit implémenté au sein même du réseau social apporte une dynamique qui favorise les échanges et la création.

## En vrac

* Réseau social
* Privé
* Portfolio
* Blog
* Votes
* Likes/upvotes
* Follow
* Feedback (request d'un user à un autre pour avoir du feed back sur une ouevre)
* Notifications
* Messagerie interne 

* Transactions financières (donation, payement, ...)
    * possibilité d'utiliser des outils externes dans un premier temps: Patreon, Paypal, Crowdfunding dans certains cas...

> "Est-ce qu'une solution polyvalente te va. C'est à dire que les utilisateurs enregistrés, ont la possibilité d'ouvir une "page" (appelons là une page pour le moment) qui leur donne la possibilité d'afficher leur créations (text, image, audio, video) sous la forme d'un blog, (des articles les uns à la suite des autres) mais avec la possibilité de sélectionner un certain nombre de leurs créations pour les mettre en avant."




## Questions

### **Système de upvotes des commentires ("comme Reddit")**

Les points clé d'un système à la reddit ou stack exchange c'est qu'il met en avant non pas le dernier poste écrit, mais les postes les plus pertinents.

Un des gros points forts c'est que ce système est dit "auto modéré". Les postes les moins pertinents ne font pas surface. 

Excellent pour stackExchange qui n'a pas vocation à être une board de discussion mais plus une encyclopédie. <span style="color:green"> Est-ce que c'est ce que tu veux ici?</span>

* [plus d'info sur ce système](https://en.wikipedia.org/wiki/Stack_Overflow)

# Applications

## Vitrine publique

Site semi statique qui contient une sélection des créations internes mise en avant. Renouvellement du contenu régulier mais globalement peu d'accès à la DB de cette partie.

## Reseau social privé

### Vue haut niveau

Hybride board / réseau social

### Features

**Fonctionnalités communautaires classiques:**
* Votes 
* likes/upvotes
* Follow
* Notifications
* Messagerie interne 
* Feeds filtrables
* Tous les objets produits par les utilisateurs sont partageable sur tous les vecteurs de communication offert à l'utilisateur

**Fonctionnalités originales:**
* Feedback (request d'un user à un autre pour avoir du feed back sur une oeuvre)
* TrueGame
* `diff`/versionning d'oeuvres 

**Fonctionnalités autres:**
* Page de profile hautement personnalisable
    * Possibilité d'afficher du contenu riche et de façon chronologique en fait un blog
    * Possibilité de définir une section "highlight" pour sticky des objets en fait une vitrine/portfolio

**Querys**:
* match geolocalisation
* match type d'activité
* ...



### Contraintes d'implémentation

#### Divers

* <span style="color:red"> Non possibilité de linker des éléments externes au site </span>
    * $\Rightarrow$ Mallus à l'expérience utilisateur
    * $\Rightarrow$ Oblige le stockage interne des ressources ($)
    * $\Rightarrow$ Compromis possible (white list) mais 
    reste un mallus à l'expérience utilisateur.




> **Commentaire perso**: Je comprends l'idée derrière cette limitation, elle favorise les échanges internes. Aussi je sais que tu ne veux pas d'un clone de FB mais attention à ne pas conffondre outil et utilisation de cet outil. Le fait que ce soit une communauté **privée** est un autre outil que tu utilise implicitement pour filtrer l'acces et donc de potentiellement avoir des utlisateurs qui utiliseront de façon intelligente les outils (partage de liens libre) à leur disposition.

#### Architecture

Architecture de type board, avec des catégories non-dynamiques (fixées à l'avance). L'utilisateur n'a pas la possibilité de créer un groupe de discussion.

<span style="color:red"> Clash avec le concepte de reseau social qui est la main app: </span> 
$\Rightarrow$ Chaque élément de contenu créé par un utilisateur est un potentiel sujet de discussion. Opposition avec le souhait de limiter les catégories de discussions.

<span style="color:red"> Il faut faire un choix d'orientation. C'est soit Facebook soit Reddit, pas les deux </span> 

> ...En attente de précision...



## TrueGame

### Vue haut niveau
Au coeur de la dynamique de la partie privée du réseau, ce jeu permet à un utilisateur de proposer une partie à un autre utilisateur (<span style="color:green">temps réel, ou différé, pas de contrainte de temps sauf si choix explicite</span>). Le premier joueur envoie une _vérité_ (déclaration ou question) formulée entre un minimum de $a$ mots et un maximum de $b$ mots. Le second joueur y répond avec les mêmes contraintes. Le jeux se termine au bout de $n$ échanges ou en fonction d'une éventuelle contrainte de temps.

### Contraintes d'implémentation

* L'objet `Partie` doit être partageable sur tout le réseau réseau 
* `Partie` est une aggrégation de `Message` qui est un objet partageable sur le réseau indépendament de sa partie mais qui garde une référence cette dernière.


### Améliorations possibles

* Obs
* Plus que deux joueurs
* Algo qui assure un certain niveau de profondeur des arguments. (Si jamais on trouve une solution à ça, cette IA sera probablement très proche du niveau d'intelligence d'un être humain et plus rien ne sera pareil après ça :D)
* Possibilité de proposer une langue pour la partie.
* Dessin à la place de texte
* Audio à la place de texte. (bonne foie pour la longueur des phrases ou algo de traitement du signal qui va compter les mots [QOS: best effort])


# Sécurité

Standards en vigueur: 
* https (ssl) ($ obligatoire)
* protection CSRF, XSS
* protection brute force et fishing (ReCaptcha, token inscription/reset mdp...)
* cryptage mdp (sha256)
