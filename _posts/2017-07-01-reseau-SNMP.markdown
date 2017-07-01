---
layout: post
title: "Reseau: Couche 7, application (fr)"
subtitle: "théorie couche 7"
date: 2017-05-15
author: Sol
category: Reseau
tags: reseau couche7  fr
finished: false
---

## 1. Architecture appplicative d'Internet

Les protocoles applicatifs classiques d'Internet sont basé sur une **architecture client/serveur** (ils utilisent soit `TCP` soit `UDP` dans la couche transport). Certains protocoles plus modèrnes offrent des échanges distribués (**P2P**) mais gardent toujours les primitives client/serveur `TCP` et `UDP` pour les échanges proprement dit. Le service doit être activé du côté serveur. Si c’est le cas un **daemon** est actif sur le port standard du service concerné (wellknown port). 

_**Un daemon** est un programme qui attend les demandes de connexion des clients et qui creée le service demandeé pour chaque nouveau client._


>### Daemon
>
>Les programmes serveurs réseau, qui doivent fonctionner en permanence, sont des daemons. C'est par exemple le cas des serveurs de messagerie. Les emails envoyés sans destinataire provoquent en général un message d'erreur provenant du serveur, avec l'adresse « mailer-daemon@serveur.exemple ».  

[<a href="/00illustrations/re_couche7/daemon.png"><img src="/00illustrations/re_couche7/daemon.png"></a>]()


>D'un coté strictement technique, un daemon sous Linux peut être n'importe quel processus ayant le processus numéro 1 comme parent (`init`). Tout process dont le parent meurt sans attendre le statut de son process enfant est adopte par `init`.
>
>_Une façon commune de lancer un daemon est donc de « fourcher » (fork) une ou deux fois et de faire arrêter le parent quand l'enfant commence ses opérations normales. Cette façon de faire est parfois résumée par la phrase fork off and dieN 1 (« fourcher et mourir » en anglais). Dans l'usage commun, un daemon peut être n'importe quel processus fonctionnant en arrière-plan, qu'il soit ou non un enfant de init._

Le client `TCP` choisit un numéro de port libre chez lui et qui est en dehors de la plage réservée (> 1023) et établit ensuite une connexion `TCP` à l'aide de l'adresse `IP` et du numéro de port standard du serveur.

Du côté serveur, la connexion est identifiée de façon unique par **les 4** !

1. adresse IP du client
2. port du client
3. adresse IP du serveur 
4. port du serveur.

> Il faut les 4 éléments car une même ordinateur client peut utiliser plusieurs fois le même service sur le même serveur. Chaque connexion (nexus?) doit être identifiable individuellement.


Contrairement aux protocoles des couches supérieures **ISO** qui définissent des messages structurés en champs (p.ex. encodés avec _BER_ (Basic Encoding Rules) en fonction d’une syntaxe abstraite comme p.ex. ASN.1, les protocoles de la couche supérieur d’Internet utilisent souvent des commandes en format **ASCII** (texte). 

Ceci permet de les simuler interactivement à partir d'un programme simple capable d'établir une session `TCP`, de lire des caractères du clavier et de les envoyer sur la connexion `TCP`. La commande `telnet` (ou nc, network cat) peut être abusée à cette fin.

Dans de nombreux cas, les protocoles Internet de couches supérieures sont de **type interrogatif**, avec **une commande envoyée par le client**, suivie d'**une réponse par le serveur**. Cette réponse est souvent **préfixée** par un code de résultat (protocoles `FTP,` `SMTP,` `IMAP,` `POP,` `HTTP`) à 3 chiffres et dont le premier indique le type du résultat. 

SMTP et HTTP échangent sur la même connexion. Parfois une connexoin supplémentaire est initiée soit par le serveur (FTP mode actif), soit par le client (FTP mode passif).

| préfixe du code d'erreur | signification |
| :-: | :-: |
| 1xx | information |
| 2xx | tout va bien |
| 3xx | qqch manque (redirection, authentification) |
| 4xx | erreur côté client (HTTP) ou erreur temporaire (SMTP) |
| 5xx | erreur côté serveur (HTTP) ou erreur permanente (SMTP) |

## 2. Protocoles applicatifs

### TELNET

Permet à un utilisateur éloigné de s’annoncer (login) sur une machine et de l’utiliser comme s’il était connecté localement sur une console texte (démarrer des programmes, éditer, etc). 

Toutes les données entrées localement sont transmises à la machine éloignée. La transmission se fait avec le format **NVT ASCII** (network virtual terminal). Ce qui signifie que le format des caractères est **ASCII** (7 bits, voir man 7 ascii sur système UNIX). Le jeu complet est supporté et si ASCII n’est pas le format local, **TELNET fait la conversion**. Les commandes qui forment de la **signalisation en-bande** sont précédées du caractère `IAC` (Interpret As Command), de valeur ASCII 255. Si un caractère ayant la valeur 255 doit être transmis tel, il est précédé du caractère `IAC` (échappement).

[<a href="/00illustrations/re_couche7/telnet.png"><img src="/00illustrations/re_couche7/telnet.png"></a>]()

Si une option n’est pas simplement activée ou désactivée mais nécessite un échange d’informations entre client et serveur, des sous-options sont utilisées (par exemple la transmission du type de terminal). **TELNET ne s’occupe pas de la partie d’identification** de l’utilisateur qui est à gérer par le daemon appelé via un dialogue interactif avec l’utilisateur. **TELNET utilise le port 23.**

#### Séquence des messages
L’échange se fait à l’aide d’une connexion TCP. Les caractères introduits par le client (**provenant du clavier de l’utilisateur**) sont souvent envoyés un à un ce qui est fort peu efficace (20 octets d’en-tête TCP, 20 octets d’en-tête IP et 14 octets d’entête Ethernet pour une charge utile de 1 octet (l’algorithme **NAGLE** est d’un précieux secours pour grouper les caractères!)

#### Evolution

`rlogin` (Remote Login) constitue une version simplifiée de `TELNET` qui ne fonctionne que entre machines `**`UNIX`**` et qui est aujourd’hui avantageusement  (sécurité) remplacée par `SSH` pour la connexion distance ou le transfert de fichiers. `SSH` incorpore de l’identification d’utilisateur, éventuellement basé sur de la cryptographie à clé révélée (clés publiques et privées), voire même des connexions en mode graphique (X11) via un tunnel.

### FTP (File Transfer Protocol)

#### Fonctions et propriétés
`FTP` est un protocole pour l’échange de fichiers entre ordinateurs éloignés. Il constitue un **sous-ensemble autonome** puisqu’il comporte sa propre phase d’identification des utilisateurs. Il permet les fonctions suivantes: 

* identification de l’utilisateur (en clair par défaut, SSL/TLS possible) 
* sélection et navigation dans les répertoires 
* création de répertoires et de fichiers 
* transfert de fichier dans les deux sens
* reprise d'un téléchargement
* effacement de fichiers 

**FTP connaît différents types de fichiers, différentes structures de fichier et différent mode de transmission.** 

En pratique, seuls les cas suivants sont importants :

* fichiers binaires: transférés sans aucune conversion (compatibilité entre systèmes **UNIX)** 
* fichiers ASCII: délimiteurs de fin de ligne des fichiers ASCII sont au besoin convertis (`LF` sous **UNIX,** `CR`/`LF` sout `Windows`)

#### Ports
<span style="color:#F92672">FTP a la particularité d’utiliser deux connexions TCP: </span>

* une pour les commandes (sur le **port 21** du serveur) 
* une pour les transferts (des ports dynamiques sont utilisés)

### Messages
FTP utilise un format de commandes codées en ASCII. Les principales commandes sont :

```
ABOR -> abandon de la dernière commande et des transfères en cours 
LIST -> liste de fichiers ou répertoires 
PASS -> mot de passe 
PORT -> adresse IP et port du client (mode actif) 
QUIT -> fin de session 
RETR -> transfert de fichier serveur → client (GET) 
STOR -> transfert de fichier client → serveur (GET) 
SYST -> type de système (serveur) 
TYPE -> typede fichier (ASCII ou binaire) 
USER -> nom de l'utilisateur
```
Les **réponses du serveur** se composent d’un **nombre à 3 chiffres** et d’un texte. **Ce genre de convention se retrouve p.ex. dans l’HTTP**. En général les deux sont affichés. Les valeurs de nombres sont partiellement normalisées. Exemple :

```
125 -> data connection already open; transfer starting
200 -> command OK
245 -> can't open data connection
```

### Séquence des messages

* La connexion est établie normalement (ouverture passive du serveur, ouverture active du client) 
* Elle dure toute la session. 
* Une connexion de transfert est établie au besoin pour chaque transfert de fichier ou pour chaque liste de fichiers (ou de répertoires)

* Elle peut être initiée soit par le
    * serveur (qui contacte le client, mode actif, ouverture active par le serveur)
    * le client (qui contacte le serveur, mode passif, ouverture active par le client). 

Ces deux modes ne sont pas toujours supportés, notamment en raison des firewalls. En mode actif, le client fournit l’adresse IP et le port à utiliser (commande **PORT**) et le serveur s’y connecte en mode client. En mode passif (commande PASV), c’est le serveur qui fournit le tuple (adresse IP, port) et c’est le **client** qui s’y connecte, ce qui est en général compatible avec les firewalls du coêé du réseau du client

## TFTP (Trivial File Transfer)

Les fonctions de FTP sont parfois trop complexes, en particulier quand le transfert se fait à l’intérieur d’un réseau local. Ces réseaux ont des délais très courts et des taux d’erreurs très bas. 

`TFTP` n’est utilisé en principe que dans les LAN (démarrage de stations sans disque, téléchargement de configurations pour des équipements, ...). `TFTP` ne permet le transfert que d’un fichier dans une direction ou l’autre et n’implémente pas d’identification de l’utilisateur. De plus, si le serveur ou le client ne supporte pas les extensions des RFC-2348 ou RFC-2349, la taille maximum transférable ne dépassera pas 32 MB ce qui est en général largement suffisant. 

**TFTP utilise UDP, port 69**

### Séquence des messages

Le protocole sûr implémenté est un IDLE REQUEST sur la base d’UDP. Cela signifie que des datagrammes individuels UDP sont envoyés, et que chaque datagramme individuel doit être confirmé par le récepteur avant de passer au suivant, ce qui explique l’**inefficacité du protocole**, en particulier hors du réseau local (sensibilité aux délais).

Les messages individuels sont :

```
read request
write request
data block
acknowlegment
```

## Email: SMTP, POP, IMAP
[www-igm.univ-mlv.fr](http://www-igm.univ-mlv.fr/~dr/XPOSE2004/abouvet/smtpPres.htm)

### SMTP (Simple Message Transfert Protocol) client / serveur

ce protocole est utilisé pour transférer les messages électroniques sur les réseaux.
Un serveur SMTP est un service qui écoute sur le **port 25**, son principal objectif est de router les mails à partir de l'adresse du destinataire.

![http://www-igm.univ-mlv.fr/~dr/XPOSE2004/abouvet/files/schemaSMTP.JPG](http://www-igm.univ-mlv.fr/~dr/XPOSE2004/abouvet/files/schemaSMTP.JPG)

il est important de connaitre les différentes phases qui se succèdent entre l'envoie d'un mail par l'émetteur et sa réception par le destinataire.
Le schéma suivant présente la succesion de ces différentes phases : 

Dans cet exemple, Fred, qui appartient au domaine truc.fr, veut envoyer un mail à Marc, qui, lui, appartient au domaine machin.com.

Fred va composer son mail sur son ordinateur puis va exécuter la commande d'envoi de son logiciel de messagerie. Le logiciel va contacter le serveur smtp du domaine truc.fr (1), c'est ce serveur qui va se charger d'acheminer (router) le mail vers le destinataire.

Le serveur smtp.truc.fr va lire l'adresse de destination du mail, le domaine du destinataire n'étant pas truc.fr, le serveur va alors contacter le serveur smtp du domaine machin.com.

Si ce serveur existe, ce qui est le cas ici, smtp.truc.fr va lui transférer le mail (2).

Le serveur smtp.machin.com va vérifier que l'utilisateur Marc existe bien dans sa liste d'utilisateurs. Il va ensuite placer le mail dans l'espace mémoire accordé aux mails de Marc sur le serveur (3).

Le mail est ainsi arrivé à destination. L'objectif du protocole SMTP est atteint.

Ensuite c'est le protocole POP qui est utilisé.
Lorsque Marc utilisera son logiciel de messagerie pour vérifier s'il a de nouveaux mails, le logiciel va solliciter le serveur pop (4) afin que celui-ci vérifie si des mails sont dans l'espace mémoire accordé à Marc (5).

S'il y a un message, le serveur pop va l'envoyer au logiciel de messagerie de Marc (6).


>Le service SMTP est divisé en plusieurs parties, chacune assurant une fonction spécifique :

* MUA : **Mail User Agent**, c’est le client de messagerie (Exemples : Outlook, ThunderBird)
    
* MTA : **Mail Transfert Agent**, c'est l'élément principal d'un serveur SMTP car c'est lui qui s'occupe d'envoyer les mails entre les serveurs. En effet, avant d'arriver dans la boite mail du destinataire, le mail va transiter de MTA en MTA. Il est possible de connaitre l'ensemble des MTA par lesquels le mail est passé, pour cela il suffit d'afficher la source du message

* MDA : **Mail Delivery Agent**, c'est le service de remise des mails dans les boîtes aux lettres (les espaces mémoires réservés) des destinataires, il intervient donc en fin de la chaine d'envoie d'un mail.

#### Commandes

Les commandes sont présentées dans l'ordre chronologique d'utilisation.

Il faut tout d'abord s'identifier auprès du serveur, on passe donc le nom de sa machine en paramètre de la commande :
```
 HELO <nom_de_machine>
```

On spécifie l'adresse de l'expéditeur du mail via la commande :
```
MAIL FROM:<adresse_email_expéditeur>
```

On indique l'adresse du destinataire du mail via la commande :
```
RCPT TO:<adresse_email_destinataire>
```

Il faut ensuite entrer le corps du mail grace a la commande suivante :
```
DATA<cr>
```
On entre alors le texte du message normalement, bien sûr aucune mise en forme n'est disponible.

Plusieurs options sont disponibles, telle que la spécification de la date d'envoie du mail :
```
Date: <date_voulue>
```

Il est aussi possible de spécifier un objet au mail :
```
Subject: <objet>
```

On peut également ajouter des destinataires en copie conforme :
```
Cc: <adresse_mail>
```

Enfin, il faut terminer le corps du mail par la commande suivante :
```
.<cr>
```

Pour clore le dialogue avec le serveur SMTP, on utilise la commande :
```
QUIT
```

#### Sécurité

> Les messages circulent en clair sur le réseau

Un des principaux défauts du protocole SMTP est que les messages circulent en clair sur le réseau.

Ainsi il est possible à l'aide d'un sniffer de capturer les trames. Celles-ci n'étant pas cryptées, on peut alors y lire les données.
Pour contrer cela de plus en plus de gens ont recours à une méthode de chiffrage de leurs mails importants.

> Les faux mails (fakemails)

Comme vu lors de la partie précédente, de part les commandes disponibles via le telnet il est possible de se faire passer pour n'importe quelle personne lors de l'envoi d'un mail.

> Le spam

Si le serveur est mal configuré, il peut servir de serveur relais.
Cette technique est très utilisée par les spammeurs qui sont toujours en quête de serveurs mal sécurisés.
Par ce biais ils peuvent envoyer leurs messages de spam tout en étant difficilement repérables.

Les sociétés qui développent les serveurs SMTP distribuent désormais leurs serveurs avec l'option de relayage désactivée.
De plus il existe des modules à rajouter à un serveur et qui permettent de stopper la diffusion de spam par le serveur (Exemple : spamassassin). 

### POP (Post Office Protocol) client / serveur

Actuellement c'est la version 3 qui est utilisée.
Le service POP écoute sur le **port 110** d'un serveur.

Le protocole POP a un objectif précis : **permettre à l'utilisateur de relever son courrier depuis un hôte qui ne contient pas sa boîte aux lettres.**

En d'autres termes, POP établie un dialogue entre le logiciel de messagerie (MUA) et la boîte aux lettres de l'utilisateur sur le serveur.

POP est avant tout un protocole très simple, de ce fait il ne propose que des fonctionnalités basiques:

* Délimiter chaque message de la boite aux lettres,
* Compter les messages disponibles,
* Calculer la taille des messages,
* Supprimer un message,
* Extraire chaque message de la boite aux lettres.

Malgrès tout, ces fonctionnalités sont amplement suffisantes pour répondre aux besoins de la plupart des utilisateurs.

POP3 présente tout de même quelque points faibles

* Sécurité: **le mot de passe circule en clair sur le réseau lors de l'établissement de la connexion avec le serveur.**
Ainsi, une personne malhonnête équipée d'un sniffer peut le récupérer et l'utiliser à mauvais escient.

* Impossibilité de choisir les messages que l'on souhaite rapatrier.
En effet, si, par exemple, on a une quarantaine de messages dans notre boite aux lettres sur le serveur, que les premiers soient du spam ou même des messages d'amis mais avec des pièces jointes assez conséquentes et que les messages qui nous intéressent soient en fin de boite aux lettres.
Si, de plus, nous n'avons qu'une connexion bas débit et de mauvaise état, c'est-à-dire des coupures régulières et des pertes de paquets aléatoires (si si, ça arrive encore de nos jours !), il nous sera alors impossible de télécharger les premiers messages et donc par conséquent les messages urgents en fin de boite aux lettres non plus.

### IMAP (Message Acces Protocol) client / serveur

la version actuellement utilisée est la 4.
Le service IMAP écoute sur le **port 143** d'un serveur.

Tout comme POP, IMAP est un protocole de récupération de mails. IMAP4 se pose donc comme une alternative a POP3.
Non seulement IMAP propose plus de services que POP, mais ceux-si sont aussi plus évolués.

Une des principales nouveautés est la possibilité de pouvoir lire uniquement les objets des messages (sans le corps).
Ainsi on peut par exemple effacer des messages sans les avoir lus.

Contrairement au protocole POP où tous les mails sont rapatriés du serveur vers le logiciel de messagerie du client, avec IMAP, les mails restent stockés dans des dossiers sur le serveur.
Ceci permet de proposer de nombreuses fonctionnalités très pratiques, telles que :

* créer des dossiers sur le serveur
* effacer déplacer des messages sans les lire éventuellement avec des règles de tri automatique
* rapatrier en local certains messages et pas d'autres en faisant une copie ou un déplacement
* lire des messages en les laissant sur le serveur
* marquer des messages sur le serveur
* recopier sur le serveur des messages qui sont en local

### Comparaison

|fonctionnalités|POP|IMAP
|:-:|:-:|:-:|
|traitement|non connecté|les deux|
|acces clients multiples|mal supporté|supporté(cache local)|
|stockage|une mailBox|une ou plusieurs|
|opérations|lecture, marquage, effacement|+ déplacement, notifications, recherche
|chiffrement|optionnel, POP3S|optionnel, IMAPS


## MIME (Multipurpose Internet Mail Extensions)

Standard qui sert à d'étendre les possibilités limitées du courrier électronique (mail) et notamment de permettre d'insérer des documents (images, sons, texte, ...) dans un courrier.

MIME propose de décrire, grâce à des en-têtes, le type de contenu du message et le codage utilisé. MIME apporte à la messagerie les fonctionnalités suivantes :

* Possibilité d'avoir plusieurs objets (pièces jointes) dans un même message
* Une longueur de message illimitée
* L'utilisation de jeux de caractères autres que le codeASCII
* L'utilisation de texte enrichi (mise en forme des messages,polices de caractères, couleurs, etc.)
* Des pièces jointes binaires (exécutables, images, fichiers audio ou vidéo, etc.), comportant éventuellement plusieurs parties

Le protocole `SMTP` classique ne permet pas de passer tous les 8 bits de données (ni de supporter de longues lignes): seul un sous-ensemble de l'ASCII 7 bit est possible (p.ex. NUL ASCII 0 est interdit).

Il faudra donc procéder à un encodage lors du transfert.

Deux encodages sont très souvent utilisés dans les protocoles Internet (p.ex. email, HTTP): **base64** et **quoted-printable**:
























<br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br>


<!--

[<a href="/00illustrations/re_couche7/email.png"><img src="/00illustrations/re_couche7/email.png"></a>]()

### SMTP (Simple Mail Transfer Protocol)

`SMTP` permet le transfer de courrier électronique entre deux **MTA** (Message Transfer Agent). La saisie des mails et leur routage ne sont pas des tâches de `SMTP`, mais celles respectivement de **MUA** (Mail User Agent) et du **MDA** (Mail Delivery Agent).

Le format de base des mail est très simple: l'entête se compose d'une ligne contenant un mot clé (To, From, Subject, Date,...) suivi par deux points(:), espace, puis une chaîne de caractères. Le corps est séparé de l'entête par une ligne vide.

Les **MTA** ajoutent une _enveloppe_ autour du message. Elle ne contient que les adresses de l'expéditeur et du destinataire du message et sont transmises, dans le protocole `SMTP`, via les commandes `MAIL FROM:` et `RCPT TO:`. Le transfer du message (comprenant d'abord l'entête, une ligne vide, puis le corps du message), initié par la commande `DATA` est terminé par une ligne ne comprenant qu'un point (une ligne de message commençant par un point doit être échappée en ajoutant un point).

On peut faire une analogie avec le courrier postal: La lettre est formée d'entêtes (expéditeur, destinataire) et d'un texte (le corps du message). C'est ce qui est transmis dans la session `DATA` et ce qui ser généralement présenté (sous forme résumée pour les entêtes) par le client mail (**MUA**). Mais une lettre est postée dans une enveloppe, qui contient un destinataire et souvent un expéditeur. C'est l'adresse de destinataire de l'enveloppe, qui sert à l'acheminement de la lettre ainsi postée (le routage est fait sur `RCPT TO:`). L'adresse d'expéditeur de l'enveloppe sert à l'acheminement d'éventuelles erreurs (**NDN**, Non Delivery Notification)

### MIME (Multipurpose Internet Mail Extensions)

Défini afin de rendre plus flexible les données transérables par `SMTP`. A l'origine, seul destextes **ASCII** pouvaient être envoyés. Grâce à `MIME` le corps d'un mail peut se composer de plusieurs parties ayant chacune un type différent. Chaque partie a son propre en-tête avec la description des types et sous-types de contenus.

[<a href="/00illustrations/re_couche7/mime.png"><img src="/00illustrations/re_couche7/mime.png"></a>]()

Le protocole `SMTP` classique ne permet pas de passer tous les 8 bits de données (ni de supporter de longues lignes): seul un sous-ensemble de l'ASCII 7 bit est possible (p.ex. NUL ASCII 0 est interdit).

Il faudra donc procéder à un encodage lors du transfert.

Deux encodages sont très souvent utilisés dans les protocoles Internet (p.ex. email, HTTP): **base64** et **quoted-printable**:

[<a href="/00illustrations/re_couche7/encodage.png"><img src="/00illustrations/re_couche7/encodage.png"></a>]()

### Envoi SMTP

La résolution des adresses e-mail en adresse IP se fait en résolvant la partie domaine (après le AT @) via `DNS` (Domain Name Server, champ MX, Mail Exchanger). Selon les entrées dans `DNS` le routage peut être direct ou indirect. Si le routage est direct une liaison `TCP` est établie entre le premier **MTA** ayant reçu le message et le **MTA** du destinataire et le mail est transféré par cette liaison. Si une entreprise utilise plusieurs **MTA** derrière un firewall, elle doit permettre un accès direct par **SMTP** à chaque machine concernée et le contrôle devient difficile. C’est pourquoi on utilise de plus en plus souvent un routage indirect. Un **MTA**, éventuellement redondant, centralise tout le courrier entrant et sortant de l’entreprise et le redistribue sur les **MTA** internes.
Le port standard utilisé est 25 (`TCP`). Les clients (`MUA`) envoyaient au début aussi sur ce port. Entre temps, la possibilité existe d’utiliser plutôt le protocole **Mail Submission**, qui lui utilise le **port 587** et est très similaire à `SMTP`. 

>Les FAIs filtrent de plus en plus le port 25 pour éviter le SPAM.

### Commandes
```
HELLO     -> Le client s'identifie auprès du serveur 
MAIL FROM -> Annonce de l'expéditeur du message      
RCPT TO   -> Annonce du destinateure du message      
DATA      -> Données du message (entête et corps)
QUIT      -> Fin de session 
VRFY      -> Contrôle de l'adresse d'un destinataire
EXPN      -> Expansion d'une liste de distribution
```

Les réponses sont composées d’un numéro et d’un texte optionnel.
A l’établissement de la connexion le serveur envoie spontanément un message d’accueil (220 xxxxxxxx xxx). Le client s’annonce ensuite avec `HELO` (ou `EHLO` pour des fonctions étendues). Des extensions (**SASL**) permettent d’identifier le client et/ou de chiﬀrer l’échange de données.

### POP et IMAP

`POP` et `IMAP` sont deux protocoles d’accès aux boîtes-aux-lettres. Les nouveaux mails sont saisis à l’aide d’un logiciel local. Les mails sortants sont en général envoyés directement via `SMTP` (**MTA** du destinataire ou **MTA** centralisé). Les mails entrants doivent être enregistrés dans une boîte-aux-lettres car le PC du destinataire final n’est pas toujours accessible et ne tourne en général pas de _daemon_ `SMTP.` Lors que celui-ci le souhaite, il s’annonce auprès de sa boîte aux lettres et relève le courrier. `POP` et `IMAP` sont prévu pour cette interface entre client mail et boîte-aux-lettres. `POP` fonctionne selon un mode hors-ligne (oﬄine). A l’établissement d’une connexion avec la boîte-aux-lettres unique, tous les mails pendants sont envoyés au client et eﬀacés du serveur (en général). Avec `IMAP,` on a le choix de la ou les boîtes-aux-lettres (une hiérarchie est présentée), et les mails sont toujours conservés sur le serveur : ils ne sont transférés au client mail qu’en cas de besoin (lecture totale ou partielle, cache). Ceci représente un grand avantage lorsqu’on lit ses mails de plusieurs endroits (ordinateur, téléphone portable). `IMAP` prévoit de plus des fonctions nécessaires à la gestion de répertoires de mails sur le serveur et propose des fonctions avancées de notification pour informer le client de l’arrivée de nouveaux messages.-->








## DNS Domain name server
sources   
[culture informatique](http://www.culture-informatique.net/cest-quoi-un-serveur-dns/)  
[siteduzero](https://openclassrooms.com/courses/apprenez-le-fonctionnement-des-reseaux-tcp-ip/le-service-dns)

### Principes
* système d'annuaire réparti
    * conversion des noms en adresse IP
    * conversion des adresses IP en noms
    * détermine le serveur responsable du courrier électronique d'un domaine

* un serveur DNS est responsable d'un ou plusieurs domaines sur lesquels il a autorité (authoritative envers ces domaines)
* l'organisme qui gère un niveau supérieur connaît les adresses des serveurs des niveaux inférieurs (delegation)
* chaque serveur connaît quelques points d'entrée situés tout en haut dans la hiérarchie


### types des serveurs

* primaire
    * détient l'autorité pour ce domaine
    * est la source des informations pour ce domaine

* secondaire
    * détient l'autorité pour ce domaine
    * contient une copie des informations du primaire

* récursif
    * répond complètement aux questions

* chache (performance)
    * chaque serveur conserve une copie des dernières réponses d'autres serveurs jusqu'à atteindre le TTL associé aux informations (danger de cache poisoning)

### déroulement des requêtes

* chaque machine a l'adresse d'un ou plusieurs serveurs DNS _sympathiques_ (récursifs)
    * en général fournit par le DHCP
    * config manuelle possible (Linux: /etc/resolv.conf)

* s'adresse à un de ces serveurs pour sa requête

* le serveur cherche dans ses informations authoritative (donc les domaines sur lesquel il fait authorité) ou dans son cache. S'il n'a rien il pose la question à d'autres serveurs jusqu'à obtenir la réponse qu'il transmet au client et qu'il store dans son cache.

    * si il est fainéant, il peut utiliser les services d'un autre serveur récursif nommé le forwarder (p.ex. DNS de l'FAI)
    * sinon, il repart aussi haut que nécessaire (p.ex. directement aux root servers si son cache est vide)

### détail des enregistrements

* une entrée dans un serveur de nom contient 4+ parties
    * le nom alphanum (hiérarchique)
    * la valeur associée
    * un type
    * une durée de vie (TTL du cache DNS)
    * un type MX: priorité du serveur mail à utiliser

>gandalf.teleinf.labinfo.eiaj.ch, 157.26.77.13, A


### priorités et round robin

Les enregistrements MX (mail) sont flanqué d'une valeur qui détermine la priorité. **Plus elle est basse, plus le serveur est prioritaire.**

```sh
host -t NS google.fr
```

Les réponses qu'on reçoit de cette commande ne sont jamais dans le même ordre à cause du **Round-Robin**: mèthode qui permet d'équilibrer la charge entre plusieurs serveurs pour ne pas les surcharger.


### types d'enregistrements

* A : c'est le type le plus courant, il fait correspondre un nom d'hôte à une adresse IPv4 

* AAAA : fait correspondre un nom d'hôte à une adresse IPv6 

* CNAME : permet de créer un alias pointant sur un autre nom d'hôte 

* NS : **définit le ou les serveurs DNS du domaine**

* MX (Mail eXchanger) : **définit le ou les serveurs de mail du domaine**

* PTR : fait correspond une IP à un nom d'hôte. Il n'est utilisé que dans le cas d'une zone inverse.

* SOA : donne les infos de la zone, comme le serveur DNS principal, l'adresse mail de l'administrateur de la zone, le numéro de série de la zone et des durées.

### Commandes Linux

`host -t (type) (nom_a_chercher) (IPserveur)`   
On peut ainsi indiquer le type de la requête (NS, A, MX, CNAME, etc.), le nom à interroger, ainsi que l'adresse IP du serveur que l'on peut préciser.

`dig`  permet un diagnostique plus poussé



### Exercices:

* Quels sont les serveurs de mail du domaine sunrise.ch et lequel sera essayé en premier?

```sh
sol@debian:~$ host -t MX sunrise.ch
sunrise.ch mail is handled by 10 mx1.smx.sunrise.ch.
sunrise.ch mail is handled by 10 mx2.smx.sunrise.ch.
```

mx1.smx... sera essyé en premier

 * quels sont les serveurs DNS du domaine he-arc.ch

```sh
sol@debian:~$ host -t NS he-arc.ch
he-arc.ch name server ns2.he-arc.ch.
he-arc.ch name server ns1.he-arc.ch.
``` 

* vous voulez configurer un serveur DNS pour effectuer l'opérations suivantes; qui contacter pour obtenir la délégation?
    * convertir www.votre-entreprise.ch en 80.83.54.2

> contacter l'autorité qui gère ch.

* pourquoi est-ce intéressant d'avoir son propre serveur de nom de type « récursif » dans une entreprise ?

> permet de bénéficier d'un cache local (performance); évite que les clients fassent des requêtes eux-mêmes, peut améliorer la sécurité si le serveur DNS est plus ou moins protégé contre les attaques (version à jour évitant le cache poisoning, sécurisation par firewall et numéros de séquences/numéros de ports aléatoires, vérification des signatures DNSSEC lorsqu'elles existent, etc); permet les réponses différentes en interne et externe par exemple.

* la résolution de big-entry.alphanet.ch coince en présence d'un firewall (pare-feu), quel est probablement le problème ?

> big-entry.alphanet.ch contient beaucoup de données, elles sont donc tronquées dans le datagramme de réponse UDP, et donc le client DNS réessaie en TCP: si le firewall de l'entreprise ne supporte pas TCP/53 mais seulement UDP/53, cela ne fonctionnera pas.










































<!--
### intro
![](http://www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/Requ%C3%AAte-Dns.png)

Dans l’exemple ci-dessus, on voit que la requête « quelle est l’adresse de www.google.fr » a répondu 74.125.230.248. Cette requête s’appelle une résolution de nom de domaine.

Si l’on poursuit, on peut constater que le serveur DNS n’est utilisé que sur la partie 1-Question et 2-Réponse. Une fois que l’ordinateur a récupéré l’adresse du serveur à joindre, il n’a plus besoin du serveur DNS.

Cette adresse IP n’est pas une adresse au hasard. Elle correspond bien à un des serveurs de Google.

les serveurs DNS fonctionnent en cascade. Pour expliquer cela on pourrait dire que lorsque qu’un serveur DNS ne connait pas la réponse, il va demander à son « parent ».

Chez vous, le serveur DNS est votre box (sauf si vous avez renseigné autre chose), et si votre box ne sait pas répondre, elle va interroger son parent : le(s) serveur(s) DNS de votre fournisseur d’accès. Cela peut remonter comme ça jusqu’aux serveurs DNS racines. 

### sécurité

* Imaginez que vous ayez entre les mains un faux annuaire (piraté) ou les numéros de téléphone ne soient pas les bons, et vous renvoie vers des pirates.

* Vous ayez besoin d’appeler votre banque : Vous appelez le numéro de téléphone et vous allez forcément tomber sur des pirates (mais vous ne le savez pas).

* Au bout du fil, on peut vous demander des informations confidentielles, et vous les donnerez en tout confiance.

Il faut bien comprendre que cela peut être pareil avec des serveurs DNS et qu’il existe des virus qui modifie vos paramètres réseaux ou votre fichier « hosts » pour faire pointer les requêtes DNS vers des serveurs pirates.

>Une des blagues des administrateurs réseaux est de modifier, au sein de l’entreprise, une des entrées DNS d’un site vers un autre site. (Par exemple : faire pointer Facebook vers le site des Tortues Ninja ! : ainsi chaque fois que quelqu’un voudra aller sur Facebook, il se retrouvera sur le site des Tortues Ninja) 

### C'est quoi un domaine?

Evidemment, sur l’internet, il n’ y a pas qu’un seul serveur DNS. L’ensemble de ces serveurs DNS constitue une sorte d’annuaire global. Les rôles des serveurs sont répartis en fonction des noms de domaines. Reprenons notre exemple téléphonique : les annuaires téléphonique sont classés par pays et pour la France par département. et bien les domaines, c’est la même chose : il s’agit d’une hiérarchie au niveau mondial. Voici quelques noms de domaines que vous connaissez certainement : « .com, .fr, .net, … » Donc ces noms vont se retrouver sur des serveurs DNS. Certains groupes de serveurs ne vont gérer que les « .com », d’autres ne géreront que les « .fr », etc … Mais comme pour le téléphone, il ne s’agit pas de la même société qui gère les numéros de téléphone en France qu’en Espagne, et bien c’est pareil pour les noms de domaine. Il existe des organismes qui sont chargés de gérer les noms de domaine. Pour le .fr, c’est l’AFNIC. (Association Française pour le Nommage Internet en Coopération).

### Comment acheter un nom de domaine ?

* Vous avez fait un superbe site Internet et maintenant vous voulez le montrer au monde entier.

* Vous voulez acheter, le nom de domaine : « je-suis-le-plus-beau.fr ». (c’est un nom de domaine de 1er niveau, nous verrons ce que cela veut dire plus loin).

* Il faut faire appel à une entreprise appelée « registrar » ou registraire de nom de domaine. (Vous n’entrez pas en contact direct avec l’AFNIC, c’est le registrar l’intermédiaire.)

* Vous allez tester que ce nom de domaine n’est pas déjà attribué à quelqu’un.

* Ensuite, vous pourrez acheter ce nom de domaine pour 1 an. (renouvelable tous les ans).

* Il faut payer

* Il ne reste plus qu’à mettre en place une entrée DNS, c’est à dire que vous allez donner au serveur DNS, l’adresse IP correspondant au nom de domaine  « je-suis-le-plus-beau.fr ».

* Lorsque les internautes taperont  » je-suis-le-plus-beau.fr », le serveur DNS les renverra vers le serveur hébergeant votre site. (Je vous ai expliqué, qu’il n’y a pas qu’un seul serveur DNS gérant le « .fr », il faudra 1 ou 2 jours pour que votre entrée DNS soit répliquée sur l’ensemble des serveurs gérant le « .fr » )

"je-suis-le-plus-beau.fr" est un domaine de 1er niveau, car il est juste après le .fr.
On lit toujours les noms de la gauche vers la droite, et la racine de tous les domaines est le « . »,
Voici comment on représente l’arborescence des domaines:

![http://www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/domaines.png](http://www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/domaines.png)


### C'est quoi un sous-domaine?

Maintenant, votre site est en ligne depuis plusieurs mois, et c’est un site incontournable. Vous enregistrez des milliers de visites par heure et votre serveur n’est plus assez costaud pour répondre à la demande. Vous décidez donc de découper votre site en plusieurs partie pour répartir celui-ci sur d’autres serveurs. (il y a d’autres solutions que le découpage du site, mais pour l’exemple, c’est celui que je vais utiliser). Vous découpez donc votre site en

* fond-ecran. je-suis-le-plus-beau.fr
* telechargement.je-suis-le-plus-beau.fr
* forum. je-suis-le-plus-beau.fr

Vous venez de créer 3 sous domaines ("fond-ecran", "telechargement" et "forum"), voila comment on va pouvoir représenter cela : 

![http://www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/sous-domaines1.png](http://www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/sous-domaines1.png)

Sur chacun des sous-domaines, vous allez pouvoir attribuer une adresse IP différente et donc pointer vers un serveur différent. Il n’est pas nécessaire de racheter un sous-domaine, cela fait partie de votre domaine. Vous gérer directement les sous-domaines auprès de votre registrar. Exemple de sous-domaines que vous connaissez (désolé de rappeler de mauvais souvenirs)  : www.impots.gouv.fr et www.economie.gouv.fr 

### Arborescence ordonnée

[partie pratique à faire!](https://openclassrooms.com/courses/apprenez-le-fonctionnement-des-reseaux-tcp-ip/le-service-dns)  

Un nom de domaine se décompose en plusieurs parties. Prenons l'exemple suivant :

> www.google.fr

* Chaque partie est séparée par un point.

* On trouve l'extension en premier (en premier, mais en partant de la droite) ; on parle de **Top Level Domain** (TLD)

* Il existe des TLD nationaux (fr, it, de, es, etc.) et les TLD génériques (com, org, net, biz, etc.).


Il existe une infinité de possibilités pour la deuxième partie. Cela correspond à tous les sites qui existent : google.fr, siteduzero.com, ovh.net, twitter.com, etc.
Comme vous le voyez, **google.fr est un sous-domaine de fr**. Le domaine fr englobe tous les sous-domaines finissant par fr.

La troisième partie est exactement comme la seconde. On y retrouve généralement le fameux **www**, ce qui nous donne des noms de domaine comme www.google.fr. www peut soit être un sous-domaine de google.fr, mais dans ce cas il pourrait y avoir encore des machines ou des sous-domaines à ce domaine, soit être directement le nom d'une machine.
Ici, **www est le nom d'une machine dans le domaine google.fr.**

On peut bien entendu ajouter autant de troisièmes parties que nécessaire, ce qui peut vous conduire à avoir un nom de domaine comme: 

> www.fr.1.new.super.google.fr.

Voici une toute petite partie de l'arborescence des noms Internet :
-->

[](https://user.oc-static.com/files/417001_418000/417424.png)

<!--
Chaque "partie" est appelée **label** et l'ensemble des labels constitue un **FQDN** (Fully Qualified Domain Name). Ce FQDN est unique. Par convention, **un FQDN se finit par un point**, car au-dessus des TLD il y a la racine du DNS, tout en haut de l'arbre. Ce point disparaît lorsque vous utilisez les noms de domaine avec votre navigateur, mais il est très important lorsque nous configurons notre propre serveur DNS.

>Au niveau DNS, www.google.frn'est pas un FQDN, car il manque le point à la fin.

>Tout FQDN sur Internet doit obligatoirement se finir par un point, comme www.siteduzero.com. qui est alors bien un FQDN, car on est sûr qu'il n'y a pas de domaine au-dessus.

Dans l'architecture du service DNS, **chaque label est responsable du niveau directement en dessous et uniquement de celui-ci**. La racine est responsable du domaine .com, le .com de google.com et google.com de www.google.com, etc. Bien entendu, Google veut gérer lui-même le domaine google.com. L'organisme qui gère le domaine .com délègue donc la gestion de ce nom de domaine à Google.

### La résolution

Vous êtes connectés à votre réseau, votre serveur DHCP vous a donné une adresse IP, un masque de sous-réseau et probablement une passerelle par défaut, ainsi qu'un serveur DNS.

Imaginez que vous entrez www.siteduzero.com dans votre navigateur. Lorsque vous entrez ce nom, **votre machine doit commencer par le résoudre en une adresse IP.**
Vous allez donc demander une résolution au serveur DNS que vous avez reçu par le DHCP. Celui-ci a deux moyens pour vous fournir la réponse :

* il connaît lui-même la réponse ;

* il doit la demander à un autre serveur, car il ne la connaît pas.

**La plupart du temps, votre serveur DNS est bien peu savant et demande à un autre serveur de lui donner la réponse.** En effet,chaque serveur DNS étant responsable d'un domaine ou d'un petit nombre de domaines, **la résolution consiste à aller chercher la bonne information sur le bon serveur.**

Nous voulons donc joindre le site www.siteduzero.com et voilà ce que va faire mon serveur DNS.
Tout d'abord, il est évident que cette information ne se trouve pas sur notre serveur, car ce n'est pas lui qui est en charge du Site du Zéro.
Pour obtenir cette résolution, notre serveur va procéder de façon rigoureuse et commencer par là où il a le plus de chance d'obtenir l'information, c'est-à-dire au point de départ de notre arborescence.

* Il va demander aux serveurs racine l'adresse IP de www.siteduzero.com. Mais comme les serveurs racine ne sont pas responsables de ce domaine, ils vont le rediriger vers un autre serveur qui peut lui donner une information et qui dépend de la racine, le serveur DNS de com.

* Il demande ensuite au serveur DNS de com l'adresse IP de www.siteduzero.com. Mais comme auparavant, le serveur com renvoie l'adresse IP du serveur DNS qui dépend de lui, le serveur DNS de siteduzero.com.

* Enfin, il demande au serveur DNS de siteduzero.com l'adresse IP de www.siteduzero.com et là, ça marche : le serveur de siteduzero.com connaît l'adresse IP correspondante et peut la renvoyer.

Maintenant, vous avez l'adresse IP de www.siteduzero.com !

>On dit qu'**un serveur fournissant la résolution d'un nom de domaine sans avoir eu à demander l'information à quelqu'un d'autre fait autorité**. 

>Les serveurs DNS utilisent un système de cache (TTL) pour ne pas avoir à redemander une information de façon répétitive, mais ils ne font pas autorité pour autant, car l'information stockée en cache peut ne plus être valide après un certain temps.

> Il n'existe pas de protocol pour convertir une IP en nom de domaine, le DNS sait faire ça aussi. On parle dans ce cas de **reverse DNS et de résolution inverse**.

### La gestion internationale des noms de domaine

Même si le système DNS n'est pas indispensable au fonctionnement d'Internet, il en est un élément incontournable.
Le système de noms de domaine est géré par un organisme américain appelé l'ICANN. Celui-ci dépend directement du Département du Commerce des États-Unis. L'ICANN est responsable de la gestion des 13 serveurs DNS qui gèrent la racine du DNS. Ces 13 serveurs connaissent les adresses IP des serveurs DNS gérant les TLD (les .fr, .com; org, etc.)

C'est l'ICANN qui autorise la création d'une nouvelle extension, comme le .xxx.
L'ICANN délègue ensuite les domaines de premier niveau à divers organismes. Pour l'Europe, c'est le RIPE qui délègue lui-même à L'AFNIC qui est responsable du domaine .fr (ainsi que des extensions correspondantes à la France d'outre-mer) ; pour le domaine .com, c'est VeriSign qui s'en occupe. Les labels inférieurs correspondent généralement à des sites ou à des entreprises, et la gestion du nom de domaine leur revient.
-->






<!--## DNS (Domain Name System)

Ce système propose:

* **un espace de noms** hiérarchique permettant de garantir l'unicité d'un nom dans unestructure arborescente.
* un système de **serveurs distribués** permettant de rendre disponible l'espace de noms.
* un système de **clients** permettant de _résoudre_ les noms de domaines (interroger lesserveurs afin de connaître l'adresse IP correspondant à un nom)


### 1. Espace de noms

La structure du système `DNS` s'appuie sur une structure arborescente dans laquelle sont définis des domaines de niveau supérieurs (appelés **TLD** Top Level Domains), rattachés à un noeud racine représenté par un point.

![http://static.commentcamarche.net/www.commentcamarche.net/pictures/internet-images-dnsarb.png](http://static.commentcamarche.net/www.commentcamarche.net/pictures/internet-images-dnsarb.png)

On appelle **nom de domaine** chaque noeud de l'arbre. Chaque noeud possède une étiquette (label) d'une longueur max de 63 char.

L'ensemble des noms de domaine constitue ainsi un arbre inversé où **chaque noeud est séparé du suivant par un point.**

L'extrémité d'une branche est appelée **hôte** et correspond à une machine ou une entité sur le réseau. Le nom d'hôte qui lui est attribué doit être unique dans le domaine considéré, ou le cas échéant dans le sous-domaine. À titre d'exemple le serveur web d'un domaine porte ainsi généralement le nom _www._



Le mot **domaine** correspond formellement au suffixe d'un nom de domaine: l'ensemble des étiquettes de noeuds d'une arborescence à l'exception de l'hôte.

Le nom absolu correspond à l'ensemble des étiquettes des noeuds d'une arborescence, séparées par des points et terminé par un point final et appelé **adresse FQDN** (Fully Qualified Domain Name). La profondeur max de l'arborescence est de 127 niveaux et la longueur maximale d'un nom FQSN est de 255 char. www.commentcamarche.net. représente une adresse FQDN

### 2. Serveurs DNS

Les machines appelès **DNS server** permettent d'établir la correspondance entre le nom de domaine et l'adresse IP des machines d'un réseau.

Chaque domaine possède un serveur DNS appelee **primary DNS server** ainsi qu'un **secondary DNS server** permettant de prendre le relais du serveur de noms primaire en cas d'indisponibilité.

chaque server DNS est déclaré à un serveur de nom de domaine de niveau immédiatement supérieur, ce qui permet implicitement une **délégation d'autorité sur les domaines**. Le système DNS est une **architecture distribuée**, où chaque entité est responsable de la gestion de son nom de domaine. Il n'existe donc pas d'organisme ayant à charge la gestion de l'ensemble des noms de domaines.

Les serveurs correspondant aux domaines les plus haut niveau (TLDs) sont appelés **root servers**. Il en existe 13 réparti dans le monde entier possédant les noms de **a**.root-server.net à **m**.root-server.net.

Un serveur DNS définit une zone, c'est à dire un ensemble de **domaines sur lequel le serveur a autorité**.



-->
