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

Les protocoles applicatifs classiques d'Internet sont basé sur une **architecture client/serveur** (ils utilisent soit `TCP` soit `UDP` dans la couche trnasport). Certains protocoles plus modèrnes offrent des échanges distribués (**P2P**) mais gardent toujours les primitives client/serveur `TCP` et `UDP` pour les échanges proprement dit.

Les protocoles applicatifs classiques d’Internet sont baseés sur une architecture **client/serveur** . Ils utilisent soit `TCP` soit `UDP` dans la couche transport. Le service doit être activé du côté serveur. Si c’est le cas un **daemon** est actif sur le port standard du service concerné (wellknown port). 

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

## Email: SMTP, POP, IMAP, MIME

[<a href="/00illustrations/re_couche7/email.png"><img src="/00illustrations/re_couche7/email.png"></a>]()

## SMTP (Simple Mail Transfer Protocol)
### Fonctions et propriétés

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

## POP et IMAP

`POP` et `IMAP` sont deux protocoles d’accès aux boîtes-aux-lettres. Les nouveaux mails sont saisis à l’aide d’un logiciel local. Les mails sortants sont en général envoyés directement via `SMTP` (**MTA** du destinataire ou **MTA** centralisé). Les mails entrants doivent être enregistrés dans une boîte-aux-lettres car le PC du destinataire final n’est pas toujours accessible et ne tourne en général pas de _daemon_ `SMTP.` Lors que celui-ci le souhaite, il s’annonce auprès de sa boîte aux lettres et relève le courrier. `POP` et `IMAP` sont prévu pour cette interface entre client mail et boîte-aux-lettres. `POP` fonctionne selon un mode hors-ligne (oﬄine). A l’établissement d’une connexion avec la boîte-aux-lettres unique, tous les mails pendants sont envoyés au client et eﬀacés du serveur (en général). Avec `IMAP,` on a le choix de la ou les boîtes-aux-lettres (une hiérarchie est présentée), et les mails sont toujours conservés sur le serveur : ils ne sont transférés au client mail qu’en cas de besoin (lecture totale ou partielle, cache). Ceci représente un grand avantage lorsqu’on lit ses mails de plusieurs endroits (ordinateur, téléphone portable). `IMAP` prévoit de plus des fonctions nécessaires à la gestion de répertoires de mails sur le serveur et propose des fonctions avancées de notification pour informer le client de l’arrivée de nouveaux messages.

## Adresses e-mail à vie

L’utilisation de noms de comptes dans les adresses e-mail est très répandue, bien que peu mnémonique – la forme longue comportant le prénom et le nom séparés par un point est souvent également utilisée. Un problème existe aussi lorsqu’on change d’employeur ou de fournisseur : il existe la possibilité de posséder une adresse à vie, parfois même gratuite, dont l’originalité est d’être indépendante de l’employeur étant donné que son rôle se limite à rediriger tout le courrier reçu sur le serveur de son choix (p.ex. sous writeme.com). Au contraire, il existe aussi des adresses dites jetables, utilisables pour s’abonner à des services sans risquer de recevoir encore plus de SPAM.

## DNS (Domain Name System)

[commentcamarche.net](http://www.commentcamarche.net/contents/518-dns-systeme-de-noms-de-domaine)