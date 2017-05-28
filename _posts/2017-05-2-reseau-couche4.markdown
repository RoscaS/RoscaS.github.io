---
layout: post
title: "Reseau: Couche 4, transport (fr)"
subtitle: "théorie couche 4"
date: 2017-05-02
author: Sol
category: Reseau
tags: reseau couche4 tcp udp fr
finished: false
---


# Couche 4: Transport

## 1. Introduction

Le rôle de la couche transport est de rendre possible la **communication logique** entre applications. En anglais, on parle de logical end-to-end communication.

> Communication logique: Communication distant dans laquelle les hôtes communicants ne sont pas forcément reliés physiquement.
> * Couche réseau (3): communication logique entre les **hôtes** 
> * Couche transport (4): communication logique entre les **processus d'applications** 

La communication logique qu'établit la couche transport se fait entre les applications impliquées dans la communication. Lorsque deux hôtes A et B communiquent, c'est par le biais d'une application. Si l'application utilisée par l'hôte A est un client de messagerie, cela veut dire que l'hôte B utilisera également un client de messagerie pour lire le message. Donc il s'agit d'une même application dans la transmission et la réception. Voilà pourquoi on parle de end-to-end application communication: La communication entre les couches se fait de façon parallèle.

![](https://user.oc-static.com/files/287001_288000/287530.png)

Ce principe reste valable pour les autres couches du modèle OSI ou TCP-IP. Chaque couche communique parallèlement avec sa couche « homologue ». La couche application de l'hôte émetteur communique directement avec la couche application de l'hôte récepteur, de même que la couche transport de l'émetteur communique directement avec la couche transport de l'hôte récepteur. (Revoir modèle OSI, encapsulation, PDU, SDU...)

#### Procédure de communication entre deux applications

Quand l'`hôte A` envoie un message, il utilise une application comme un client de messagerie. Cette application lui donne accès aux services réseaux pour envoyer un mail, donc le protocole `SMTP` sera utilisé, par exemple. Une fois ce SDU reçu par la couche transport, il sera converti en un PDU, comme nous l'avons vu. La couche transport, qui est aussi responsable de la fragmentation des unités de données, va « couper » ce SDU en plusieurs chaînes (ou morceaux) qu'on appelle « chunks » en anglais. À chaque « morceau » sera ajouté un en-tête. Le SDU reçu par la couche transport sera donc « brisé » en 4 PDU distincts, par exemple.


[Analogie enfantine qui envoie du lourd](/reseau/reseau-analogie-c4-5.html)

## 2. Rôles de la couche 4

* Communication orientée connexion (Connection-oriented communication): ce service est bénéfique pour plusieurs applications, notamment parce qu'il interprète la connexion établie entre processus comme étant un flux de données. Ce flux de données n'est en fait qu'une séquence d'octets. Ainsi, il faudrait donc gérer l'orientation des octets, qui est également un service de cette couche. **Il est plus facile de gérer ce genre de communication que celles en mode orienté non connexion.**

* L'ordre de livraison : nous savons que la couche transport fragmente les données en plusieurs séquences. **La couche réseau ne peut pas garantir que les données envoyées arriveront exactement dans l'ordre même de leur envoi** (contrôle de séquences). La couche de transport s'assure que l'ordre de livraison est le même que l'ordre d'envoi. Cette gestion de séquences se fait en utilisant **un numéro de séquence**.

* Fiabilité : Le but de ce service est de vérifier l'intégralité des paquets car ces derniers peuvent se perdre en cours de transmission à cause de la **congestion du réseau**. Cette vérification d'intégralité se fait souvent en utilisant un code de détection d'erreurs tel que la somme de contrôle (`checksum`). La fiabilité consiste aussi à s'assurer que le récepteur reçoit chaque segment qui lui est envoyé. Cela est accompli par l'envoi des `ACK` et `NACK` (accusés de réception) à l'émetteur. Il faudrait donc également être en mesure de **renvoyer les séquences qui se sont perdues** : cela se fait par une requête automatique de renvoi.

* Contrôle de congestion : Sert à contrôler le trafic dans un réseau (informatique ou de télécom) afin de réduire la congestion (saturation) d'un réseau en évitant que les autres clients utilisent les liens de connexion qui sont déjà extrêmement utilisés.

* Multiplexing / demultiplexing : C'est ce que permettent les ports de communication des applications. Plus d'explications à suivre...

## 3. Ports

* Adresse IP: Adresse unique assignée à un hôte pour permettre de distinguer chaque hôte dans des réseaux.

* Adresse physique (MAC): Adresse associée à la carte réseau d'un hôte pour distinguer les hôtes dans un réseau local.

Ces deux adrèsses servent toutes les deux à identifier un hôte.

>Un numéro de port sert à adentifier une application. 

Le numéro de port nous permet de faire la distinction entre les applications. Nous savons déjà que la couche transport établit une communication bout-à-bout entre les processus d'applications. Le numéro de port va noue permetre de distinguer les nombreux processus d'une application qui sont en fait des services exécutés sur une machine C'est le numéro de port qui permettra de les différencier.

Par exemple dans le cas de la transmission d'un mail, le premier service ou la première application utilisée est un `MUA`. Le `MUA` utilise le protocole `SMTP` pour envoyer le mail au serveur de messagerie souvent en passant par un `MSA`. Dans l'ordre de réception, on utilisait également un autre `MUA` pour retirer le mail du serveur de messagerie avec un protocole de réception comme `IMAP` ou `POP`. Il se peut donc que **les deux services (`POP`/`IMAP` et `SMTP`) soient exécutés au même moment sur une même machine hôte**. C'est là qu'intervient le numéro de port, parce qu'il nous permet de faire la distinction entre les services qui ont été demandés par l'application distante, qu'il s'agisse d'un serveur de messagerie ou d'un client de messagerie. Le protocole `SMTP` utilise le protocole `TCP` pour la transmission au numéro de port 25.

L'organisme [IANA (Internet Assigned Numbers Authority)](https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority) classe les numéros de port en trois catégories principales:

**Well-known ports** (0 - 1023):
Réservés pour des services bien connus (web, envoi de mail, ...) 

**Ports réservés** (1024 - 49151): 
Utilisés par les applications propriétaires.


**Ports dynamiques** (49152 - 65635):
Ports libre qu'on peut utiliser pour nos applications.

* Echo (7): ICMP requst ? **à vérifier**
* FTP (21): Protocole de transfert de fichier
* Telnet (23): Utilisé pour l'établissement des sessions à distance
* HTTP (80): (www)  Protocole de téléchargement (principalement de pages web)
* SNMP (161):   
* Discard (9):  
* SSH (22): Protocole permettant l'échange de données par le biais d'un canal sécurisé
* SMTP (25): Protocole d'envoi de courrier électronique
* WHOIS (43): protocol  Protocole ou service utilisé pour l'identification d'une machine par son nom de domaine ou son adresse IP. La procédure d'identification se fait par une requête envoyée à un des registres Internet pour obtenir des informations.
* POP3 (10): Protocole de retrait de mails d'un serveur de messagerie
* HTTPS (443): HTTP sécurisé
* DNS ( 53): Protocole de résolution des noms de domaine

[liste plus complète](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)

> Un numéro de port est un nombre entier codé sur 16 bits (2 octets) et peut donc avoir une valeur allant de 0 à 65 335


## 4. Les notions autour du numéro de port

Les ports sont un concept très important en réseau. Nous allons voir quelques-unes des notions qui gravitent autour des ports.

### La redirection de port (port forwarding)

Intimement lié à la translation des adresses réseaux (`NAT`).Le port forwarding ou encore port mapping permet à un hôte d'un réseau distant (Internet par exemple) de communiquer avec un hôte d'un réseau local en faisant transiter les paquets TCP/IP par un serveur `NAT`. `NAT` peut être un service installé sur une machine, un serveur ou un routeur. Généralement une machine combine `NAT` et pare-feu. 

### Le scan de port (port scanning)

Le scan ou balayage de port est une technique très populaire en réseau et surtout en sécurité. Cette technique consiste à « scanner » un hôte afin de découvrir les ports ouverts et d'avoir la liste des services exécutés sur cet hôte. 

Connaître les ports ouverts et les services exécutés permet à un hacker, par exemple, d'exploiter les vulnérabilités des services afin de planifier une attaque. 

Le port scanning peut viser plusieurs cibles. Les cibles les plus visées sont le 
* `SYN` (on parle de SYN scanning),
* `TCP` (TCP scanning)
* `UDP` (UDP scanning)
* `ACK` (ACK scanning)
* `FIN` (FIN scanning) et Window (Window scanning).

### Déclenchement de port (port triggering)

Le port triggering consiste, comme son nom l'indique, à déclencher quelque chose. Cette technique permet de déclencher l'ouverture d'un port précis. Or, qui dit déclencher dit élément déclencheur, aussi appelé **événement**. 

Le port triggering ne se produit donc que lorsqu'un événement particulier a lieu. 
On le configure au niveau du matériel assurant le service `NAT`. Grâce à cette configuration, on peut ouvrir un port entrant lorsque l'hôte utilise un port sortant. 

Le port triggering est en quelque sorte une **automatisation du port forwarding**. Si le trafic sortant sur des ports précis (prédéterminés par une configuration) provoque du trafic entrant sur des ports d'entrée prédéterminés, on peut automatiquement les forwarder à l'hôte alors que les ports entrants sont utilisés.

### PAT : Port Address Translation

`PAT` joue presque le même rôle que `NAT`. Le concept reste le même : il permet à plusieurs hôtes dans un réseau local d'utiliser une même adresse IP d'un réseau public. On l'utilise dans le même but que le subnetting, c'est-à-dire pour mieux gérer les adresses disponibles. 

`PAT` est une technique qui opère dans les couches 3 et 4 du modèle OSI, alors que `NAT` s'arrête à la couche 3.

L'un des défauts de cette technique est la complexité que cela implique au niveau du pare-feu. Étant donné que les hôtes d'un réseau local partagent tous ou sont tous « déguisés » en une seule adresse publique, les hôtes externes au réseau local (les hôtes distants) ne peuvent pas établir une connexion avec un hôte interne au réseau sans utiliser une technique complexe consistant à traverser le service `NAT` ou forwarder les connexions via un port déterminé dans le pare-feu.

> Tous ces services fonctionnent grace aux **sockets**.

## 5. Sockets

Nous savons maintenant que les protocoles se chevauchent: 

>Un **protocole applicatif (couche 7)** (`SMTP`, `POP`, `HTTP`, etc)

peut être interfacé à un 

> **protocole de transport (couche 4)** (`UDP`, `TCP`). 

Mais comment faire pour que les processus des applications communiquent avec les ports des protocoles de transport ? 

C'est à ça que servent les sockets. Un socket est une interface entre les processus: en réseau, un socket sert donc à faire communiquer un processus avec un service qui gère le réseau. 

Chaque socket a une adresse de socket. Cette adresse est constituée de deux choses: 

* une adresse IP 
* un numéro de port 

C'est grâce à la programmation de socket que l'on définit le modèle de communication:

* Si le socket a été configuré de manière à envoyer ou recevoir, c'est un modèle **Half-Duplex**

* S'il a été configuré de manière à envoyer et recevoir simultanément, il s'agit d'un modèle **Full-Duplex**

Étant donné que les sockets sont en fait une **interface de programmation d'applications** (**API**), on peut donc s'en servir pour programmer des applications en réseaux (par exemple, créer une application pour faire communiquer un client et un serveur). 

Voici un schéma illustrant une communication entre un client et un serveur:

![](https://user.oc-static.com/files/287001_288000/287537.png)

Le client commence à se connecter au serveur grâce aux sockets. 

Une fois la connexion établie (étape 1), le client et le serveur peuvent communiquer (s'échanger des messages). C'est ce qui se passe dans les étapes 2 et 3. À la fin de la communication, le client envoie une demande de terminaison de session au serveur (étape 4) et le serveur met fin à la connexion.

Le serveur n'effectue pas les mêmes actions initiales que le client. 

Le serveur utilise les sockets pour lier un port d'application à son processus correspondant. Ensuite, il _écoute_ ce port. 

**Écouter**, veut dire: « continuellement vérifier s'il y a un événement qui se passe sur ce port précis ». Ce faisant, il va découvrir qu'un client essaie de se connecter à lui par le numéro de port qu'il écoute. Il accepte donc la requête, établit la connexion (étape 1) et, finalement, les deux communiquent (étapes 2, 3 et 4)
. 

Le modèle de communication est Half-Duplex parce que le client envoie et attend la réponse du serveur et vice-versa.

## 6. Les fonctions des **API**

Comme vous le savez dorénavant, la programmation des sockets se fait par le biais d'une **API** (Interface de Programmation d'Application). Il existe plusieurs **API** pour programmer les sockets ; l'une des plus populaires est Winsock. Cependant, chaque **API** propose les fonctions suivantes :


`socket()`: dans le schéma, vous voyez bien que la première case est « socket ». Cette fonction crée un objet de type Socket que l'on peut identifier par un nombre entier. La création de cet objet, bien entendu, nécessite l'allocation de ressources (mémoire) pour cet objet.

`bind()`: en français, ça veut dire « lier ». Au niveau du serveur, dans le schéma, nous avons mis « liaison » juste après socket. Ainsi, après avoir créé une nouvelle instance d'un objet de type Socket, au niveau du serveur, il faut utiliser la fonction `bind()` pour lier ou associer un socket à une adresse de socket (IP + port).

`listen()`: cette fonction est également utilisée au niveau du serveur. Dans le schéma, c'est le bloc « écoute ». Cette fonction change l'état du socket et le met dans un état d'écoute. Comme nous l'avons expliqué, le serveur va « écouter » l'adresse à laquelle est associé le socket en attendant un événement. Il y a également une fonction poll() qui agit aussi dans le même but que la fonction listen(), mais d'une manière différente.

`connect()`: cette fonction permet au client d'établir une connexion avec le serveur. En général, ça sera une connexion `TCP`, car la majorité des sockets utilisent `TCP` comme protocole de transmission. Cette fonction assigne également un numéro de port local au socket coté client.

`accept()`: en toute logique, cette fonction sera appelée du côté du serveur, car elle sert principalement à accepter une requête de connexion envoyée par le client.

`send()`: cette fonction, qui signifie « envoyer » et qui est également représentée dans le schéma (bloc « envoi »), sert à envoyer des données du client au serveur et vice-versa. On utilise également la fonction `write()` ou `sendto()`.

`recv()`: cette fonction, représentée par le bloc « réception » du schéma, sert à recevoir les données envoyées par la fonction send(). On utilise également la fonction read() (lire) ou recvfrom() (recevoir de).

`close()`: c'est la fonction qui permet au système d'exploitation de libérer les ressources allouées lors de la création de l'objet de type Socket avec la fonction socket(). C'est donc la terminaison de la connexion. Elle est représentée par le bloc « fin » dans notre schéma.

## 7. Multiplexing/demultiplexing

![](https://upload.wikimedia.org/wikipedia/commons/e/e0/Telephony_multiplexer_system.gif)

> Multiplexage: Le fait d'encapsuler les SDU en spécifiant le numéro de port des processus des applications qui font l'envoie (SDU devient PDU) et le transmettre à la couche réseau.

> Démultiplexage: Le fait de transmettre un PDU donné au processus d'une application donnée.


![](https://user.oc-static.com/files/287001_288000/287620.png)

voir [analogie enfantine](/reseau/reseau-analogie-c4-5.html)

Les `processus` (cousins) écrivent des lettres (`SDU`). Un `protocole de transport` (Pierre), qui peut ètre `TCP` ou `UDP`, açomplit une opération de **multiplexage** en rangeant chaque `SDU` dans son enveloppe (**encapsulation**). Nous nous retrouvons avec 12 `PDU` (12cousins ont écrit des lettre). Le protocole `TCP` ou `UDP` transmet ces `PDU` au protocole `IP` (le bureau de poste). Une fois que ces lettre ont emprunté un média de transmission (câble, wifi...), elles vont arriver au niveau de la couche réseau de l'hôte récepteur. Là, églement, le protocole réseau `IP` va faire son job. Une fois son travail terminé, IP va faire appel à un protocole de transport (Jean), lequel effectuera une opération de **démultiplexage** en transmettant chaque enveloppe à son destinataire.

>TCP et UDP sont responsables de la modification des en-têtes des unités de données lors du multiplexage / démultiplexage

## 8. Port source et port destination

Un hôte A a une instance d'une application HTTP en cours d'exécution (disons qu'il surfe sur le web) et qu'il envoie une requête (il demande une page) à un serveur web B qui a plusieurs instances du service HTTP en cours d'exécution. Chaque instance a le même numéro de port, soit 80.

**Les serveurs web créent un nouveau processus pour chaque requête HTTP qu'ils reçoivent. Un serveur qui gère 10 requêtes gère donc 10 processus utilisant tous le même numéro de port.**

Alors, comment le protocole de transport au niveau du serveur web va réussir son démultiplexage (acheminer la requête au processus adéquat) ? Il y a plusieurs processus exécutés au même moment. En se basant uniquement sur un seul numéro de port dans le segment de transport, le démultiplexage va échouer. :o

Nous avons donc besoin de deux numéros de port (source et destination) avec des valeurs différentes.

En réseau, la communication entre deux hôtes se fait selon une **architecture client-serveur**. 

>Celui qui initialise la transmission est le client

>Celui qui répond est le serveur. 

Ainsi, si nous avons un hôte junior0-PC qui essaie de communiquer avec un hôte claude-PC via une session à distance (`Telnet`), quelles seront les valeurs des champs « Source port » et « Destination port » ? Eh bien, le champ « Destination port » aura pour valeur 23, car c'est le numéro de port bien connu, conventionnellement attribué par l'IANA pour Telnet. Mais quid du champ « Source port » ? Quelle valeur allons-nous y mettre ? 23 ?

En général, le protocole de cette couche (UDP ou TCP) va générer automatiquement un numéro de port qu'aucun processus n'utilise actuellement. Comme vous le savez, plusieurs processus (parfois plusieurs processus d'un même type d'application) peuvent être exécutés au même moment sur une même machine. **Chaque processus a un numéro de port**. On ne peut pas utiliser un numéro de port déjà utilisé par un autre processus pour le champ « Source port ».

> Si, grâce à la programmation des sockets, vous avez créé une application qui doit utiliser un numéro de port de votre choix, alors, bien entendu, vous pouvez déterminer le numéro de port source à utiliser, et ce dernier sera marqué dans le champ « Source port » du segment. Pour ce faire, utilisez la fonction bind() de l'API que vous aurez utilisée pour la création de votre application.

Disons que nous avons programmé une application de manière à utiliser le numéro de port `Y`. Nous voulons utiliser notre application et envoyer une requête HTTP à un serveur web. Nous savons que le serveur écoute (grâce à la fonction `listen()` ) le port 80. Le message que nous envoyons au serveur web sera par exemple l'URL d'un site web : www.siteduzero.com (c'est simplifié ; une requête HTTP est plus complexe, en réalité).

![](https://user.oc-static.com/files/287001_288000/287644.png)

Y sera la valeur du champ « Source port » et 80 la valeur du champ « Destination port ».

Le serveur va recevoir ce PDU et examiner sa constitution. « Ah tiens, c'est une requête d'une page web (port 80) par l'application x (port Y) ». Il va traiter la requête et renvoyer une réponse. C'est là qu'on comprend l'importance des deux numéros de port.

Dans la procédure d'envoi de la requête, le champ « Source port » avait pour valeur Y. Dans l'envoi de la réponse, le champ « Source port » prend la valeur du champ « Destination port » de la requête, et le champ « Destination port » prend la valeur du champ « Source port » de la requête. Ainsi, notre segment envoyé par le serveur web ressemblera à ceci :

![](https://user.oc-static.com/files/287001_288000/287645.png)


[suite]()