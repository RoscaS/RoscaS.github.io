---
layout: post
title: "Reseau: SNMP"
subtitle: "Simple Network Management Protocol"
date: 2017-07-01
author: Sol
category: Reseau
tags: reseau 
finished: false
---

## Sources
[Mkina-corpus](https://makina-corpus.com/blog/metier/2016/initiation-a-snmp-avec-python-pysnmp)  
[wikipedia](https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol)
[pySnmp](http://pysnmp.sourceforge.net/docs/snmp-design.html)

## 1. Introduction

`SNMP` signifie Simple Network Management Protocol. C'est un protocole de gestion de réseau. Concrètement il permet de **superviser** l'état des équipements réseaux ou d'un parc informatique, aussi bien que le materiel que les services:

* Routeurs, switchs
* Stations de travail
* Imprimantes
* Services (messagerie, ftp, ssh, http, processus, mémoire, ...)

>Par **supervision** il faut comprendre "obtenir des informations" sur l'état de ces "éléments". Ce protocole permet aussi d'effectuer des actions de maintenance comme redémarrer un service ou une machine, nettoyer les têtes de lecture d'une imprimante, ...

### Fonctionnement

SNMP se base sur l'idée qu'un système de supervision de réseau se compose de

* Noeuds à administrer (**Managed Nodes**), contenant chacun un **agent**.
    * **agent** $$ \Rightarrow $$ service qui dialogue avec les managers pour échanger des informations sur l'état du noeud.
* D'au moins une station d'administration, le manager (**Network Management Station**)
* D'un protocole permettant d'échanger de l'information entre les agents et le **NMS** ce protocole est `SNMP`

![alt](http://pysnmp.sourceforge.net/_images/nms-components.svg)

* Le manager envoie des requêtes de type `GET` aux agents pour récuperer de l'information sur un service/une machine via le protocole SNMP
* Il peut aussi envoyer des requêtes `SET` pour modifier l'état des services et/ou de la machine gérés par l'agent
* Les agents peuvent envoyer des requêtes de type `TRAP` au manager pour signaler un dysfonctionnement
* Les agents peuvent être regroupés au travers d'un **agent maître**
* Les informations accessibles et fournies par les agents sont organisées dans une base "virtuelle" appelée **MIB**. Cette base est normalisée

![alt](https://upload.wikimedia.org/wikipedia/commons/thumb/2/26/SNMP_communication_principles_diagram.PNG/1000px-SNMP_communication_principles_diagram.PNG)

### Pourquoi utiliser SNMP?

* Protocole ancien supporté par presque tous les constructeurs de matériel réseau
* Depuis la version 2, il est **sécurisé**, offrant un accès restreint à certaines informations et s'appuie sur des protocoles de chiffrement comme `SSH` et `TLS` (mais avec ses propres librairies et outils internes).
* L'information contenue dans les **MIBs** est normalisée: quelque soit l'appareil et son constructeur, une même information sera accessible au même endroit et de la même manière. 
> Vous êtes l'administrteur réseau et vous voulez connaître la mémoire totale disponible d'une machine, vous l'obtiendrez de la même façon quelque soit l'OS (mac/W/Unix/android) et le matériel utilisé (AMD, ARM, Intel,...).
* Il est simple (les agents sont légers, la complexité est déportée au niveau des managers)
* Indépendant de l'architecture réseau et des éléments (Pc, imprimante, routeurs, ...)
* Extensible, on peut personnaliser les bases d'informations en plus de ce qu'offre le standard
* Robuste: S'appuie sur `UDP` **ou** `TCP`: fonctionnera encore lorsque le réseau aura de graves problèmes d'engorgement (passe en `UDP`).

### Meta

Globlement ce protocole est très pratique, et est très répandu. Il  peu de concurrents matures mais il souffre de certains points négatifs:

* Parfois, il n'est pas simple: Les **MIBs** sont "lisibles par l'homme"  _mais ceux qui arrivent à le lire ne sont pas tous vraiment humains_

* Le protocole est simple mais son implémentation peu être complexe

* La version 3 a implémenté sa propre couche `SSH` plutôt que de faire comme `HTTPS` (du `HTTP` over `SSH`). Ceci rend pénible les communications: il n'y a pas d'auto-négociation pour le chiffrement et vous devez vous-même fournir 5 informations pour créer une connexion sécurisée

* Certains concurrents commencent à voir le jour comme `WMI` et offrent une langage d'interrogation plus riche (**WQL**)

### Historique

* La première version du protocole date de 1989: Elle n'offre **pas de sécurité** et utilise l'**MIB v1**  qui reste assez simple
* La seconde version a été publiée en 1993
    * $$ \Rightarrow $$ plus de sécurité tant au niveau de l'authentification que du chiffrement
    * $$ \Rightarrow $$ permet l'administration distribuée
    * $$ \Rightarrow $$ apporte une version plus riche de la **MIB** (MIB-2)
* La version 3 a été publiée en 1998
    * $$ \Rightarrow $$ apporte des transactions chiffrées via `SSH`
    * $$ \Rightarrow $$ plus modulaire

### Dans les faits

* La version 2 a eu du mal à démarrer et plusieurs "releases" ont été faites. C'est la version "2c" qui a su s'imposer. Plus d'informations:
    * [presentation SNMP](https://www.digitalocean.com/community/tutorials/an-introduction-to-snmp-simple-network-management-protocol)
    * [PySNMP](http://pysnmp.sourceforge.net/docs/snmp-history.html)
* La version 3, tout comme la v2 a eu du mal à s'imposer aussi. Elle n'est toujours pas supportée par tous les matériels aujourd'hui même si cela s'est bien amélioré. Les problèmes de sécurité étant devenu vraiment sensibles de nos jours, elle est la **version recommandée**.
* `SNMP` domine le monde TCP/IP et il est le **standard recommandé** depuis mai 1990

## 2. Les MIBs

La **Management Information Base** est une base de données "virtuelle" permettant d'organiser de manière hiérarchique les informations fournies par les agents sur les équipements.

Ce n'est pas une base de données à proprement parler comme une base relationnelle. Elle n'existe qu'au travers de l'agent qui est libre de l'implémenter comme il le souhaite. Quand l'agent ne fonctionne pas, il n'y a pas de données dans la **MIB**. Seule la description de la MIB (un fichier texte) existe.

>Une **MIB** décrit en fait comment numéroter/accéder à l'information.

<span style="color:red">C'est l'agent qui stock  (**mais on devrait dire gère**) comme il veut cette information, laquelle est le plus souvent "volatile".</span>

>Il est très important de bien comprendre le point précédent, c'est ce qui gêne souvent la compréhension des **MIBs** par les nouveaux arrivants sur ce protocole.

Prenons l'exemple du nombre de processus actifs sur une machine. Celui-ci est identifié de façon unique par la **MIB-2**. Mais cette information est demandée par l'**agent** au système d'exploitation au moment ou il reçoit une requête pour cette dernière, car cette donnée est changeante à chaque instant. Elle n'est donc pas "stockée" par l'OS. Elle n'est pas accessible au travers d'une requête `SQL`. Elle est fournie quand on la demande , et tant que l'agent ne l'a pas demandée lui-même à l'OS, elle n'est pas vraiment disponible ou n'a pas encore d'existence.

1. Le manager demande l'information à l'agent
2. L'agent reçoit la demande et appelle une primitive système pour l'obtenir
3. L'agent retourne l'information au manager

C'est une information "volatile" qui n'est pas vraiment stockable.

>Note perso: en gros, l'agent demande une information qui a une certaine relevance à un moment T.

Beaucoup d'informations issues des **MIBs** sont de cette nature, d'autres sont parfois stockées quelque part, comme le responsable d'une machine, le port d'un service ou encore l'état d'une ligne élecrtique ou d'une diode (éteinte, allumée). 

Selon les constructeurs/OS ces informations sont stockées physiquement différemment, mais la **MIB** les normalise pour la grande majorité afin qu'une même information soit toujours demandée de la même manière par le manager. **C'est à l'agent de savoir ou aller la chercher** selon le matériel/service géré.

>Cela apporte beaucoup de facilités dans l'écriture de sondes/outils de supervisions. C'est une des grandes forces de ce standard.

### MIB-2

Une des **MIB** les plus connues est la **MIB-2**, décrite par la _RFC 1213_. Elle est mise en oeuvre dans la presque totaltalité des équipements TCP/IP. Elle compte 10 groupes:

* système
* interfaces
* Adresse Translation
* IP
* ICMP
* TCP
* UDP
* EGP
* transmission
* SNMP

C'est a dire que toute information que vous pourriez demander concernant la **MIB-2** sera classée dans l'un de ces groupes. Des exemples d'informations que vous pourriez retrouver dans ces ensembles, sont:

* liste des processus (avec info mémoire et CPU)
* débits réseaux
* load average
* utilisation mémoire
* contact du système $$ \Rightarrow $$ uptime
* utilisation de l'espace disque
* tables de routage

### Exemple de descitpion d'une MIB

Les mibs sont décrites dans un "langage" appelé  **ASN.1**. L'exemple suivant présente l'information "**descitpion d'interface** (réseau)" telle que décrite en **ASN.1**:

```
ifDescr OBJECT-TYPE
 SYNTAX DisplayString (SIZE (0..255))
 ACCESS read-only
 STATUS mandatory
 DESCRIPTION
 "A textual string containing information about the
 interface. This string should include the name of
 the manufacturer, the product name and the version
 of the hardware interface."
 ::= { ifEntry 2 }
 ```

 La lecture de la définition de cette donnée est assez compréhensible par elle-même:

 * nom: `ifDescr`
 * type: `DisplayString`: une string de 255 char max
 * mode d'accès: "lecture seule"
 * son état (`STATUS`): "obligatoire"
 * enfin son emplacement dans le noeud parent `ifEntry`: 2ème sous-élément du noeud `ifEntry`

 ### Organisation de l'information dans les MIBs

 Pour bien comprendre cette description "hiérarchique" de la donnée `ifDescr`, voici un graph représentant la hiérarchie principale de toutes les MIBs:

 ![alt](https://makina-corpus.com/blog/metier/2016/pysnmp/snmp_mib.png)

 Sous le noeud racine, noté `.` se trouvent 3 informations numérotées:

 * `CCITT` avec le numéro 0
 * `ISO` avec le numéro 1
 * `ISO - CCITT` avec le numéro 2  

 Sous le noeud `ISO` se trouvent 4 informations, elles aussi numérotées:

 * standard, avec le numéro 0
 * registration authority, avec le numéro 1
 * member body, avec le numéro 2
 * organization, avec le numéro 3

 Le chemin d'accès à l'information `organisation` située sous `ISO`, elle-même située sous la racine sera `.1.3` ou encore `.ISO.organzation`.

 Par exemple, l'accès à l'information **MIB-2** se fera via le chemin:
 ```
.1.3.6.1.2.1
 ```
 ou encore
 ```
.ISO.organisation.DoD.Internet.management.MIB-2
 ```

#### En gros

Les **MIBs** définissent des informations en **ASN.1** lesquelles sont accessibles via un chemin exprimé avec des numéros ou les noms des informations parentes.

### Les OID

Les informations sont donc numérotées selon leur ordre dans l'arbre de la **MIB**, ces numéros sont appelées **OID** (Object IDentifier). Voici quelques exemples d'**OIDs**:

```
.1.3.6.1.2.1.1 system : uptime, contact, nom
```
```
.1.3.6.1.2.1.2 interfaces: interfaces réseau
```
```
.1.3.6.1.2.1.4 ip: ip routing, etc
```
```
.1.3.6.1.2.1.5 icmp: icmp errors, discards... (icmp c'est par exemple le ping)
```
```
.1.3.6.1.2.1.6/7 tcp ou udp: états des connexions tcp ou udp
```

La question devient maintenant "**Comment trouver toutes les informations desponibles dans une MIB?**"
Les réponses seront:

* En lisant les standards et normes associés [rfc1213](https://tools.ietf.org/html/rfc1213)
* En exécutant des requêtes avec des outils `SNMP` qui permettent de parcourir toute l'arborescence d'une **MIB**
* En parcourant ces **MIBs** via des sites Internet qui ont l'amabilité de les représenter facilement, comme le site [IP Monitor](http://www.commentcamarche.net/download/telecharger-34059278-ipmonitor)

On observera sur ce dernier que le chemin complet à l'information `ifDescr` est fournit par l'**OID** `1.3.6.1.2.1.2.2.1.2`. C'est ce que va nous permettre de fire **PySNMP**: Envoyer des requêtes demandant la valeur d'un noeud (une information) située à un emplacement précis dans une **MIB** ou modifiant cette valeur.

Et c'est tout...

### Un univers de MIBs

Il exite beaucoup de **MIBs** différentes:

* Selon le matériel (routeur, imprimante...) les informations sont différentes
* Pour cette raison, certains équipements ou constructeurs fournissent donc leurs propres **MIB** définissant une branche dans l'arbre des **OIDs** avec un numéro officiel défini auprès de l'**IANA** (Internet Assigned Numbers Authority). On retrouve par exemple **CISCO** à cette branche de l'arbre: `iso.org.dod.internet.private.entreprise.cisco` = `1.3.6.1.4.1.9`. A cet emplacement, `CISCO` est libre de proposer les **MIBs** décrivant les informations de ses propres matériel comme il l'entend. Beaucoup d'autres sociétés en font autant, comme Alcatel/Nokia pour ne citer qu'eux.

> Les sites/logiciels comme **IP monitor** présentent de nombreuses MIB pour différents types d'informations/matériel/services

## 3. Les communautés

L'accès aux informations d'une **MIB** est filtré par ce qu l'on appelle la "communauté", _sauf version 3 du protocole_. La "communauté" est une sorte de de mot partagé, "public" par défaut. "public" peut-être rapproché du login "anonymous" disponible sur la plupart des serveurs `ftp`.

**Une demanade d'informations est toujours exécutée via une communauté**. Selon la communauté utilisée, les informations reçues seront différentes:

* la communauté "public" donne accès au niveau "read-only"
* il existe un niveau privé en lecture/écriture (communauté "private") qui est le plus souvent désactivé (il donne accès à toute la configuration système en écriture)

Donc quand nous souhaitons accéder à/ou modifier une information d'un agent, on:

* envoie une requête `GET`/`SET` à l'agent
* en fournissant le chemin de l'information désirée, son **OID**
* en fournissant la communauté utilisée ("private" pour les modifications)
* en fournissant l'adresse IP/nom de domaine ou s'exécute l'agent
* le port utilisé est par défaut le port `161`

## 4. Utilisation du protocole en ligne de commande

Pour bien comprendre ce que permet ce protocole, nous allons exécuter les commandes de base avec des **outils systéme**. Ceci permettra de toucher du doigt la mécanique sous-jacente et de vérifier que les agents comprennent bien nos roquêtes avant de les écrire en Python.

### Les commandes

Requêtes proposées par le protocole pour permettre aux manager et agents de s'échanger de l'information:

* `GET`$$ \Rightarrow $$ demande la valeur d'un **OID** par le manager à l'agent. L'agent retourne l'information via un message de type `RESPONSE`
* `GETNEXT` $$ \Rightarrow $$ demande l'information suivante dans la **MIB**. Permet de parcourir une **MIB** sans connaître ce qu'elle contient
* `SET` $$ \Rightarrow $$ modifie la valeur associée aa un **OID**
* `GETBULK` $$ \Rightarrow $$ demande tout un ensemble d'infos au manager contenant l'information demandée
* `RESPONSE` $$ \Rightarrow $$ message envoyé par l'agent au manager contenant l'information demandée
* `TRAP`: $$ \Rightarrow $$  message envoyé par l'agent au manager pour signaler un problème 
* `INFORM` $$ \Rightarrow $$ msg envoyé par le manager. Confirme réception du `TRAP`.

### Prérequis

Pour lancer nos premières commandes, il convient de disposer d'un agent et d'un manager. 
Pour le manager, nous pouvons installer les outils `snmp` et `mibs` de base via ces commandes

```bsh
sudo apt-get install snmp snmp-mibs-downloader
sudo apt-get install smitools libsmi
```
Pour trouver un agent, nous pouvons:

* Essayer avec une imprimante réseau ou une machine possédant une adresse Ip. Généralement elles sont compatibles avec les protocoles `SNMP`
* Installer notre propre agent en suivant le tutoriel suivant: [how to install and configure an SNMP Daemon and client (linux)](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-an-snmp-daemon-and-client-on-ubuntu-14-04)
* **Lancer nos commandes vers l'agent `demo.snmplabs.com` librement accessible via Internet.**

> Si nous ne disposons pas de la possibilité d'installer les commandes `SNMP` sous Linux, il est bon de savoir que la librairie `PySNMP` propose des scripts équivalents en pur Python qu'on peut installer avec la commande `pip install pysnmp-apps`

### La commande GET

Accepte plusieurs paramètres dont:

* `-v` $$ \Rightarrow $$ version du protocole
* `-c` $$ \Rightarrow $$ communauté
* `-O` $$ \Rightarrow $$ format de sortie (a,f,n,s,...)

**exemple**

Avant tout il faut commenter la ligne contenant `mibs :` dans `/etc/snmp/snmp.conf`


```
sol@debian:~/Downloads/Python-3.6.1$ snmpget -v2c -c public -Oa demo.snmplabs.com sysDescr.0
SNMPv2-MIB::sysDescr.0 = STRING: Linux zeus 4.8.6.5-smp #2 SMP Sun Nov 13 14:58:11 CDT 2016 i686

```

La version du protocole à utiliser est obligatoire, dans la commande précédente, nous utilisons le protocole version `2c` (`v2c`) et la communauté `public`. Nous demandons l'information `sysDescr`. Pour ce faire nous ajoutons le chemin `.0` qui désigne la valeur du noeud `sysDescr`.

Les autres valeurs sous un noeuds (`.1.2`, etc...) désignent les sous éléments de ce dernier. Les informations peuvent donc être demandées via le chemin complet comme sous la forme d'un `OID` numérique `1.3.6.1.2.1.1.1.0` (**ou textuel ou encore via le nom court**. $$ \Rightarrow $$ normalement unique).

Même commande avec l'**OID**:

```
sol@debian:~/Downloads/Python-3.6.1$ snmpget -v2c -c public -Oa demo.snmplabs.com 1.3.6.1.2.1.1.1.0
iso.3.6.1.2.1.1.1.0 = STRING: "Linux zeus 4.8.6.5-smp #2 SMP Sun Nov 13 14:58:11 CDT 2016 i686"
```

### La commande GETNEXT

Elle accepte les mêmes paramètres que `GET` mais retourne non pas l'`OID` demandé mais celui qui suit dans l'arborescence. En chainant un `GET` puis plusieurs `GETNEXT` on peut donc consulter tout l'arbre d'une `MIB`.

```
sol@debian:~/Downloads/Python-3.6.1$ snmpgetnext -v2c -Oa -c public -Os demo.snmplabs.com sysDescr.0
sysObjectID.0 = OID: netSnmpAgentOIDs.10
```

On peut exécuter `GETNEXT` sur n'importe quel noeud, donc la racine d'une **MIB** mais parcoutir tout un arbre est laborieux.

### Les commandes GET BULK et WALK

Pour éviter d'envoyer plusieurs commandes `GET` les unes après les autres ce qui surcharge les échanges réseau, il est possible de demander à consulter plusieurs `OID` en une seule fois via la commande `GET BULK`. **Mais tous les agents ne le supportent pas**.

On peut aussi utiliser la commande `WALK` pour parcoutir toute une arborescence, comme on le ferait avec `GET` et `GET NEXT`. Mais c'est la commande `WALK` qui les exécute pour nous.

```
sol@debian:~/Downloads/Python-3.6.1$ snmpwalk -v2c -On -c public -Os demo.snmplabs.com system
sysDescr.0 = STRING: Linux zeus 4.8.6.5-smp #2 SMP Sun Nov 13 14:58:11 CDT 2016 i686
sysObjectID.0 = OID: netSnmpAgentOIDs.10
sysUpTimeInstance = Timeticks: (274672133) 31 days, 18:58:41.33
sysContact.0 = STRING: SNMP Laboratories, info@snmplabs.com
sysName.0 = STRING: new system d
sysLocation.0 = STRING: San Francisco, California, United States
sysServices.0 = INTEGER: 72
sysORLastChange.0 = Timeticks: (274672261) 31 days, 18:58:42.61
sysORID.1 = OID: snmpFrameworkMIBCompliance
sysORID.2 = OID: snmpMPDCompliance
sysORID.3 = OID: usmMIBCompliance
sysORID.4 = OID: snmpMIB
sysORID.5 = OID: tcpMIB
sysORID.6 = OID: ip
sysORID.7 = OID: udpMIB
sysORID.8 = OID: vacmBasicGroup
sysORDescr.1 = STRING: The SNMP Management Architecture MIB.
sysORDescr.2 = STRING: The MIB for Message Processing and Dispatching.
sysORDescr.3 = STRING: The management information definitions for the SNMP User-based Security Model.
sysORDescr.4 = STRING: The MIB module for SNMPv2 entities
sysORDescr.5 = STRING: The MIB module for managing TCP implementations
sysORDescr.6 = STRING: The MIB module for managing IP and ICMP implementations
sysORDescr.7 = STRING: The MIB module for managing UDP implementations
sysORDescr.8 = STRING: View-based Access Control Model for SNMP.
sysORUpTime.1 = Timeticks: (1506) 0:00:15.06
sysORUpTime.2 = Timeticks: (1506) 0:00:15.06
sysORUpTime.3 = Timeticks: (1506) 0:00:15.06
sysORUpTime.4 = Timeticks: (1506) 0:00:15.06
sysORUpTime.5 = Timeticks: (1506) 0:00:15.06
sysORUpTime.6 = Timeticks: (1506) 0:00:15.06
sysORUpTime.7 = Timeticks: (1506) 0:00:15.06
sysORUpTime.8 = Timeticks: (1506) 0:00:15.06
```

### Traduire les OIDs

La commande `snmptranslate` permet de traduire des `OID` du mode numérique au mode texte et inversément

```
sol@debian:~/Downloads/Python-3.6.1$ snmptranslate -On SNMPv2-MIB::sysDescr.0
.1.3.6.1.2.1.1.1.0

sol@debian:~/Downloads/Python-3.6.1$ snmptranslate .1.3.6.1.2.1.1.1.0
SNMPv2-MIB::sysDescr.0
```

### Modifier un OID, la commande SET

La commande `snmpset` permet de modifier la valeur d'un `OID`. Elle n'est disponible qu'avec le protocole **v2** et la communauté `private` ou le protocole **v3**.

L'exemple suivant est fourni par le protocole v3. Les paramètres utilisés sont:

* `-u` $$ \Rightarrow $$  utilisateur
* `-l` $$ \Rightarrow $$  niveau de sécurité pour s'identifier
* `-a` $$ \Rightarrow $$  protocole d'authentification (chiffrement mdp)
* `-A` $$ \Rightarrow $$  mot de passe d'authentification
* `-x` $$ \Rightarrow $$  algorithme de chiffrement pour la connexion
* `-X` $$ \Rightarrow $$  mot de passe/salt pour le chiffrement de la connexion

C'est là qu'on découvre le côté **pénible du protocole v3** qui implémente lui-même les connexions `SSH` sans faire de `SNMP` over `SSH`: Il n'y a pas d'auto-négociation des algorithmes de chiffremen, il faut tout préciser par sois-même. <span style="color:red"> Vrmt chiant...</span>

<span style="color:red"> N'a pas fonctionné chez moi. </span> 
```
$ snmpget localhost sysLocation.0
SNMPv2-MIB::sysLocation.0 = STRING: Rouans
$ snmpset -u demo -l authPriv -a MD5 -x DES -A pass1 -X pass2 localhost sysLocation.0 s "Earth"
SNMPv2-MIB::sysLocation.0 = STRING: Earth
$ snmpget localhost sysLocation.0
SNMPv2-MIB::sysLocation.0 = STRING: Earth
```

### Recommandations concernant l'utilisation du protocole SNMP et outils associés

Les versions 1 et 2 du protocole sont insuffisamment sécurisées (pas du tout pour la première)

La protection dans la v2 du protocole se fait uniquement via les `communautés` qui n'utilisent pas de mdp. Ce n'est pas très fiable comme type de protection, car il suffit de connaître le nom de la communauté pour accéder aux informations d'un matériel qui pourrai s'avérer être sensible.

Les agents de Windwos XP étaient particulièrement réputés pour proposer dans leur configuration par défaut beaucoup trop d'informations sensibles, comme les logins des utilisateurs qui étaient accessibles directement via la communauté `public`.

Dans la pratique l'agent fournit par les outils NetSNMP (sous linux) est relativement lent et n'est pas forcément très bien sécurisé, beaucoup de failles sont découvertes régulièrement.

Aussi quelque soit la solution retenue, désactivez systématiquement le mode anonyme, ou alors contrôlez chaque information proposée. Puis créer des `ACLS` d'accès à chaque `MIB` par utilisateur afin d'avoir une maîtrise complète de qui peut faire quoi.

Enfin,  <span style="color:red"> il est très important de privilégier la version 3 du protocole ! </span> 


