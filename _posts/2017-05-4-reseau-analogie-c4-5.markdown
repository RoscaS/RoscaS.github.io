---
layout: post
title: "Reseau: relation couche 7,4,3 (fr)"
subtitle: "Analogie enfantine qui pèse!"
date: 2017-05-04
author: Sol
category: Reseau
tags: reseau couche4 couche3 couche7 udp tcp fr
finished: true
---
Retour au [poste principale](/reseau/reseau-couche4.html)  
Source: [openclassrooms](https://openclassrooms.com/courses/les-reseaux-de-zero/exploration-de-la-couche-transport-1-2-1)



# Scénario

Dans notre scénario, il y a deux maisons géographiquement opposées : l'une à l'est et l'autre à l'ouest. Chaque maison héberge une douzaine d'enfants. Dans chacune des maisons vivent des familles, de telle sorte que les enfants de la maison à l'est (appelons-la Maison-Est) et ceux de la maison à l'ouest (Maison-Ouest) soient cousins.

Chacun des enfants d'une maison écrit à chacun des enfants de l'autre maison, chaque semaine. Chaque lettre est mise dans une enveloppe. Ainsi, pour 12 lettres, nous avons 12 enveloppes. Les lettres sont envoyées par le biais de la poste. Nous avons donc un taux de transmission de 288 lettres par semaine (12 enfants x 12 lettres x 2 maisons). Dans la Ville-Est (celle dans laquelle se trouve Maison-Est), il y a un jeune du nom de Pierre. Dans la Ville-Ouest (celle dans laquelle se trouve Maison-Ouest), il y a un jeune homme du nom de Jean.

Pierre et Jean sont tous deux responsables de la collecte des lettres et de leur distribution dans leurs maisons respectives : Pierre pour Maison-Est et Jean pour Maison-Ouest.

Chaque semaine, **Pierre et Jean rendent visite à leurs frères et sœurs, collectent les lettres et les transmettent à la poste**.
Quand les lettres arrivent dans la Ville-Est, Pierre se charge d'aller les chercher et les distribuer à ses frères et sœurs. Jean fait également le même travail à l'ouest.

![](https://user.oc-static.com/files/287001_288000/287531.png)

>Les cousins de Maison-Est écrivent des lettres, Pierre (le grand frère) les collecte et les envoie à la poste de sa ville. La poste de la Ville-Est enverra les lettres à la poste de la Ville-Ouest. Jean va les chercher à la poste et les distribue aux cousins de la Maison-Ouest.

Dans le schéma, nous avons fait en sorte que ces « blocs » soient dans un ordre qui explicite la procédure de transmission. Mais, en fait, pour mieux comprendre cela, il faut suivre la structure semblable au modèle OSI ou TCP-IP, donc une structure de pile (couches superposées). Ceci étant, nous allons réarranger ces blocs ou étapes (qui sont en fait des couches) de manière à retrouver la structure du modèle OSI. Cela donne ceci :

![](https://user.oc-static.com/files/287001_288000/287533.png)

Comme vous pouvez le voir, nous avons mis _Médias de transport_ entre les deux postes. **Un média, c'est un moyen**(de transport). Les deux postes ne sont pas dans la même ville, alors comment les lettres seront-elles envoyées ? Par le train ? Par avion ? Il y a **plusieurs moyens disponibles**.

Dans cet exemple, le bureau de poste pourvoit une **connexion logique entre les deux maisons**. Le service du bureau de poste transporte les lettres de maison en maison et non de personne en personne directement, car il y a un intermédiaire.

Pierre et Jean établissent une **communication logique entre les cousins** (les enfants de Maison-Est et les enfants de Maison-Ouest). Ils collectent et distribuent les lettres à leurs frères et soeurs. Si nous nous mettons dans la perspective des cousins, Pierre et Jean jouent le rôle du bureau de poste, bien qu'ils ne soient que des contributeurs. Ils font partie du système de transmission des lettres ; plus précisément, ils sont le bout de chaque système.

Que vous le croyiez ou non, cette analogie explique très bien la relation qu'il y a entre la couche réseau et la couche transport, ou plutôt l'inverse pour respecter l'ordre.

### Analogie

* Les maisons, dans notre exemple, représentent les hôtes dans un réseau.
* Les cousins qui vivent dans les maisons représentent les processus des applications.
* Pierre et Jean représentent la couche transport (un protocole de cette couche). Ce sera soit `UDP`, soit `TCP`.
* Le bureau de poste représente la couche réseau des modèles de communication (plus précisément, un protocole de cette couche). Le plus souvent, c'est le protocole IP qui est utilisé.

![](https://user.oc-static.com/files/287001_288000/287532.png)

Ainsi, nous pouvons remarquer que le début de la communication (l'écriture des lettres) à l'est commence par des processus d'une application (les cousins). C'est normal, nous sommes dans la couche application. Ensuite, nous nous retrouvons avec plusieurs PDU (les enveloppes) qu'un protocole (`TCP` ou `UDP`) de la couche transport (Pierre) prendra et transmettra à un protocole de la couche réseau (le bureau de poste).

Dans la procédure de réception, un protocole de la couche réseau (le bureau de poste) dans le réseau (la ville) de l'hôte récepteur (Maison-Ouest) recevra les PDU (enveloppes) qui proviennent d'un protocole de la couche réseau (le bureau de poste) du réseau (la ville) de l'hôte émetteur.

Ce protocole va donc lire les en-têtes et donner les PDU à un protocole de transport (Jean). Ce dernier va transporter les données à l'hôte récepteur (Maison-Ouest) et les distribuer aux processus d'applications (cousins).

Comme vous pouvez le comprendre à partir de cette analogie, Pierre et Jean ne sont impliqués que dans des tâches précises qui concernent leurs maisons respectives. Par exemple, ils distribuent les lettres et les transmettent à la poste. Mais ils n'ont rien à voir avec les activités propres à la poste, telles que le tri des lettres. Il en est de même pour les protocoles de transport. Ils sont limités au « bout » des échanges. Ils transportent les messages (enveloppes) d'un processus à la couche réseau et de la couche réseau à un processus. Ce sont des fonctions « bout-à-bout » (end-to-end).

### Différents protocoles

Nous allons supposer qu'un jour, Pierre et Jean partent en vacances. Ils se feront donc remplacer par une paire de cousins, disons Jacques et André. Étant donné que ces deux derniers sont nouveaux, ils n'ont donc pas assez d'expérience dans la gestion et la distribution des lettres. Par conséquent, il peut arriver que, de temps en temps, ils fassent tomber des enveloppes en les emmenant à la poste ou en allant les chercher. Donc, de temps en temps, il y a des pertes d'informations (lettres). Puisqu'ils ont du mal à s'habituer, ils seront plus lents que Pierre et Jean. Donc la transmission des lettres à la poste et leur distribution aux cousins prendront un peu plus de temps.

Il se trouve donc que la paire Jacques et André n'offre pas le même modèle de service que la paire Pierre et Jean.

Par analogie, dans un réseau informatique, il est tout à fait possible qu'il y ait plusieurs protocoles de transport. D'ailleurs, vous savez d'ores et déjà que les plus utilisés sont `TCP` et `UDP`. Ces protocoles de transport offrent des modèles de services différents entre applications. Il est important de noter que la qualité des services offerts par Pierre et Jean dépend étroitement de la qualité de service offerte par le bureau de poste. C'est logique, en fait !
Si la poste met trois jours, par exemple, pour trier les courriers reçus, est-ce que Pierre et Jean peuvent être tenus responsables de ce délai ? Peuvent-ils transmettre les lettres aux cousins plus vite que ne peut trier la poste ? Non, bien entendu. :) 
De même, dans un réseau informatique, la qualité de service du protocole de transport dépend de la qualité de service du protocole de réseau. Si le protocole de la couche réseau ne peut garantir le respect des délais pour les PDU envoyés entre les hôtes, alors il est impossible pour les protocoles de transport de garantir le respect des délais de transmission des unités de données (messages) transmises entre applications (processus d'applications).

La qualité de service offert par Pierre et Jean dépend étroitement de la qualité des services offerts par la poste, mais l'inverse n'est pas vrai. C'est logique.

### Un exemple:

Jean, par exemple, peut garantir à ses cousins que, coûte que coûte, « vos lettres seront transmises ». Mais la poste peut perdre des lettres lors du tri, par exemple. ;) Jean a offert un service fiable alors que la poste a offert un service non fiable. 

Un autre exemple est que Jean peut garantir à ses frères qu'il ne lira pas leurs lettres. Mais qui sait ce qui se passera à la poste ? Peut-être qu'un type pourrait ouvrir une enveloppe, lire la lettre, la refermer et la transmettre. Pire encore, au niveau de la poste, l'emplyé peut lire, faire une photocopie et envoyer l'original mais garder une copie. Il a brisé la confidentialité de la transmission. Ceci est un concept très important en sécurité. Un protocole de transmission peut offrir des services de cryptage pour protéger et garantir la confidentialité des unités de données mais ces dernières peuvent être interceptées au niveau de la couche réseau par l'attaque de l'homme du milieu (MITM, Man In The Middle attack), par exemple.

### Conclusion

Nous avons vu à quoi servait la couche transport, nous avons vu la différence entre la communication logique offerte par la couche de transport et celle offerte par la couche de réseau. Introduction au principe d'une communication fiable et non fiable.

**Ce qu'il faut retenir est que la couche transport établit une communication logique entre processus tandis que la couche réseau établit une communication logique entre hôtes.**

Retour au poste principale [poste principale](/reseau/reseau-couche4.html)  