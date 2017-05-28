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