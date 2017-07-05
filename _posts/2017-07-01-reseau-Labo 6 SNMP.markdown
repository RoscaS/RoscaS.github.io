---
layout: post
title: "Reseau: Labo 6 SNMP & Docker"
subtitle: ""
date: 2017-07-03
author: Sol
category: Reseau
tags: reseau 
finished: false
---

## todo

[client http Python](https://www.junian.net/2014/07/simple-http-server-and-client-in-python.html)

## Sources
* [explication de snmp + tuto Python](https://makina-corpus.com/blog/metier/2016/initiation-a-snmp-avec-python-pysnmp)
* [oidview](http://www.oidview.com/mibs/0/HOST-RESOURCES-MIB.html)
* [client http Python](https://www.junian.net/2014/07/simple-http-server-and-client-in-python.html)
* [Layman's Guide to a Subset of ASN.1, BER, and DER]( http://luca.ntop.org/Teaching/Appunti/asn1.html)  
* [man](https://manpages.debian.org)



# Premiers pas avec SNMP

## Objectifs
* interroger un agent `SNMP` avec des outils `UNIX` (modèle PULL **client-serveur** classique)
* concepts de base de la **MIB**
* modèle `PUSH`: les traps

## 1. Installation

## 2. ?

## 3. Experimentation

### snmpget

```
sol@debian:~$ snmpget -v 1 -c public 157.26.77.13 system.sysName.0
SNMPv2-MIB::sysName.0 = STRING: gandalf
```

<a href="/00illustrations/labo6/wireshark1.png"><img src="/00illustrations/labo6/wireshark1.png"></a>

Nous voyons ici que l'information system.sysname.0 est répertoriée par l'**OID** `1.3.6.1.2.1.1.5.0` qui est équivalente à `.ISO.organization.DoD.Internet.management.MIB-2.system.sysName.0` en suivant l'arbre sur l'illustration suivante:

![alt](https://makina-corpus.com/blog/metier/2016/pysnmp/snmp_mib.png)


### snmpwalk

Ici nous voyons le **10 premières réponses** à la commande `snmpWalk` avec en argument l'ip `157.26.77.13` en **community** `public`

```
sol@debian:~$ snmpwalk -v 1 -c public 157.26.77.13
SNMPv2-MIB::sysDescr.0 = STRING: Linux gandalf 3.10-0.bpo.2-amd64 #1 SMP Debian 3.10.5-1~bpo70+1 (2013-08-11) x86_64
SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (113849707) 13 days, 4:14:57.07
SNMPv2-MIB::sysContact.0 = STRING: Marc SCHAEFER <schaefer@alphanet.ch>
SNMPv2-MIB::sysName.0 = STRING: gandalf
SNMPv2-MIB::sysLocation.0 = STRING: Salle rack SI Campus Arc 2 NE
SNMPv2-MIB::sysServices.0 = INTEGER: 72
SNMPv2-MIB::sysORLastChange.0 = Timeticks: (1) 0:00:00.01
SNMPv2-MIB::sysORID.1 = OID: SNMP-FRAMEWORK-MIB::snmpFrameworkMIBCompliance
```

<a href="/00illustrations/labo6/wireshark2.png"><img src="/00illustrations/labo6/wireshark2.png"></a>

Sur la capture Wireshark nous voyons que cette commande est une automatisation de la commande `getnext`. Cette commande parcours toute l'arborescence de la **MIB** accessible à l'adresse ip: `157.26.77.13` Nous voyons aussi les **OIDs** des services correspondant aux prints de la commnde sur la capture Wireshark.


### snmpdf

```batch
sol@debian:~$ snmpdf -v 1 -c public 157.26.77.13
Description                    size (kB)       Used  Available  Used%
Physical memory                  8185000    6570972    1614028    80%
Virtual memory                  20047012    7191564   12855448    35%
Memory buffers                   8185000     469740    7715260     5%
Cached memory                    2823684    2823684          0   100%
                                       0          0          0     0%
Swap space                      11862012     620592   11241420     5%
/dev                               10240          0      10240     0%
/                               19092180   12343960    6748220    64%
/boot                             233191      38735     194456    16%
/var/lib/lxc                   103081248   35433252   67647996    34%
/var/lib/lxc/15/rootfs/scratch  61796348   32560380   29235968    52%
/sys/fs/cgroup                         0          0          0     0%
/sys/fs/fuse/connections               0          0          0     0%
/backup                        287702408  195458304   92244104    67%
```

<a href="/00illustrations/labo6/wireshark3.png"><img src="/00illustrations/labo6/wireshark3.png"></a>

J'ai tenté de décripter l'échange qui a un format interessant. 

Les 15 premières requêtes sont une "reconnaissance" à la façon `walk` et reçoivent en réponse des `int` qui correspondent à la valeur du dernier élément de l'**OID** suivant.

Les 14 requêtes suivantes contiennent 4 informations:
* une valeur `OctetString`
* un `int` (toujours 1024)
* un élément de la colone `size(kB)`
* un élément de de la colone `Used` sur la même ligne que la valeur `size(kB)`

<span style="color:red"> à continuer, ça cloche à partir de la ligne 44 </span> 


## 4. Questions

### a. 
Selon le schéma suivant (_architecture de SNMP_), identifier chacun des partenaires (qui est celui qui interroge? qui est celui qui répond?).

<a href="/00illustrations/labo6/diag1.png"><img src="/00illustrations/labo6/diag1.png"></a>

* Le **manager SNMP** envoie des `get - request` et `set - request` à un **agent SNMP** qui lui répond avec des `get - response` `set - response` après avoir consulté une **MIB**.
* L'**agent SNMP** peut envoyer des requêtes de type `trap` pour généralement signaler un dysfonctionnement.
* Ces échanges se font via le protocole **SNMP**

### b.
Quel est le protocole de couche 4 utilisé? Quel est le numéro de port de l'agent et quel est le numéro de port du manager?

* L'agent reçoit des requêtes en **UDP** sur le **port 161**
* Le manager peut envoyer des requêtes **UDP** de **n'importe quel port disponible** sur le port 161 de l'agent et la réponse de l'agent se fera sur le port utilisé par le manager.
* Les requêtes de type `trap` (et `informRequests`) sont envoyées de **n'importe quel port disponible de l'agent** et arrivent sur le **port 162** du manager.

Les **PDU** SNMP des captures Wireshark spécifient le protocole **UDP** de plus nous voyons également sur les précédentes captures Wireshark qu'aucune connexion n'est faite avant un échange.

<a href="/00illustrations/labo6/udp.png"><img src="/00illustrations/labo6/udp.png"></a>

Précision:
>Although SNMP works over TCP and other protocols, it is most commonly used over UDP that is connectionless – both for performance reasons, and to minimize the additional load on a potentially troubled network that protocols like TCP impose. The design of the Simple Network Management Protocol was optimized for repairing sick networks, rather than doing heavy things with perfectly healthy ones.

[en.wikipedia.org](https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol)

### c.
Avez-vous eu besoin de vous identiﬁer (donner un login)?   
* Non

De vous authentiﬁer (donner un mot depasse) ?    
* Non

Les données sont-elles en clair?   
* Oui (pas de cryptage)

Existe-t-il un moyen d’améliorer cela avec un agent compatible?   
* Possibilité de sécuriser avec `SSH` (SNMPv3 over SSH) et `TLS` et `DTLS` (SNMPv3 over TLS/DTLS)
* Il semble que le SNMPv3 over SSH sois récent. Initialement SNMPv3 implentait lui même sa propre couche SSH.

### d.
Quelles sont les principales requêtes et réponses du protocole?

* `getRequest`
* `setRequest`
* `getNextRequest`
* `getBulkRequest`
* `Reponse`
* `Trap`
* `InformRequest`  

Voir mon [article](/reseau/reseau-SNMP.html/) sur le protocole snmp pour plus d'infos

### e. 
Dans quels champs de la MIB HOST-RESOURCES est-ce que la commande `snmpdf` ci-dessus va chercher?

`1.3.6.1.2.1.25.2.3.x` $$ \Rightarrow \; \large x \in  [1,38] $$

```
sol@debian:~$ snmpwalk -v 1 -c public 157.26.77.13 1.3.6.1.2.1.25.2.3
HOST-RESOURCES-MIB::hrStorageIndex.1 = INTEGER: 1
HOST-RESOURCES-MIB::hrStorageIndex.3 = INTEGER: 3
HOST-RESOURCES-MIB::hrStorageIndex.6 = INTEGER: 6
HOST-RESOURCES-MIB::hrStorageIndex.7 = INTEGER: 7
HOST-RESOURCES-MIB::hrStorageIndex.8 = INTEGER: 8
HOST-RESOURCES-MIB::hrStorageIndex.10 = INTEGER: 10
HOST-RESOURCES-MIB::hrStorageIndex.31 = INTEGER: 31
HOST-RESOURCES-MIB::hrStorageIndex.32 = INTEGER: 32
HOST-RESOURCES-MIB::hrStorageIndex.33 = INTEGER: 33
HOST-RESOURCES-MIB::hrStorageIndex.34 = INTEGER: 34
HOST-RESOURCES-MIB::hrStorageIndex.35 = INTEGER: 35
HOST-RESOURCES-MIB::hrStorageIndex.36 = INTEGER: 36
HOST-RESOURCES-MIB::hrStorageIndex.37 = INTEGER: 37
HOST-RESOURCES-MIB::hrStorageIndex.38 = INTEGER: 38
HOST-RESOURCES-MIB::hrStorageType.1 = OID: HOST-RESOURCES-TYPES::hrStorageRam
HOST-RESOURCES-MIB::hrStorageType.3 = OID: HOST-RESOURCES-TYPES::hrStorageVirtualMemory
HOST-RESOURCES-MIB::hrStorageType.6 = OID: HOST-RESOURCES-TYPES::hrStorageOther
HOST-RESOURCES-MIB::hrStorageType.7 = OID: HOST-RESOURCES-TYPES::hrStorageOther
HOST-RESOURCES-MIB::hrStorageType.8 = OID: HOST-RESOURCES-TYPES::hrStorageOther
HOST-RESOURCES-MIB::hrStorageType.10 = OID: HOST-RESOURCES-TYPES::hrStorageVirtualMemory
HOST-RESOURCES-MIB::hrStorageType.31 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.32 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.33 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.34 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.35 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.36 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.37 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.38 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageDescr.1 = STRING: Physical memory
HOST-RESOURCES-MIB::hrStorageDescr.3 = STRING: Virtual memory
HOST-RESOURCES-MIB::hrStorageDescr.6 = STRING: Memory buffers
HOST-RESOURCES-MIB::hrStorageDescr.7 = STRING: Cached memory
HOST-RESOURCES-MIB::hrStorageDescr.8 = STRING: Shared memory
HOST-RESOURCES-MIB::hrStorageDescr.10 = STRING: Swap space
HOST-RESOURCES-MIB::hrStorageDescr.31 = STRING: /dev
HOST-RESOURCES-MIB::hrStorageDescr.32 = STRING: /
HOST-RESOURCES-MIB::hrStorageDescr.33 = STRING: /boot
HOST-RESOURCES-MIB::hrStorageDescr.34 = STRING: /var/lib/lxc
HOST-RESOURCES-MIB::hrStorageDescr.35 = STRING: /var/lib/lxc/15/rootfs/scratch
HOST-RESOURCES-MIB::hrStorageDescr.36 = STRING: /sys/fs/cgroup
HOST-RESOURCES-MIB::hrStorageDescr.37 = STRING: /sys/fs/fuse/connections
HOST-RESOURCES-MIB::hrStorageDescr.38 = STRING: /backup
HOST-RESOURCES-MIB::hrStorageAllocationUnits.1 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.3 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.6 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.7 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.8 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.10 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.31 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.32 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.33 = INTEGER: 1024 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.34 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.35 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.36 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.37 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.38 = INTEGER: 4096 Bytes
HOST-RESOURCES-MIB::hrStorageSize.1 = INTEGER: 8185000
HOST-RESOURCES-MIB::hrStorageSize.3 = INTEGER: 20047012
HOST-RESOURCES-MIB::hrStorageSize.6 = INTEGER: 8185000
HOST-RESOURCES-MIB::hrStorageSize.7 = INTEGER: 1998532
HOST-RESOURCES-MIB::hrStorageSize.8 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageSize.10 = INTEGER: 11862012
HOST-RESOURCES-MIB::hrStorageSize.31 = INTEGER: 2560
HOST-RESOURCES-MIB::hrStorageSize.32 = INTEGER: 4773045
HOST-RESOURCES-MIB::hrStorageSize.33 = INTEGER: 233191
HOST-RESOURCES-MIB::hrStorageSize.34 = INTEGER: 25770312
HOST-RESOURCES-MIB::hrStorageSize.35 = INTEGER: 15449087
HOST-RESOURCES-MIB::hrStorageSize.36 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageSize.37 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageSize.38 = INTEGER: 71925602
HOST-RESOURCES-MIB::hrStorageUsed.1 = INTEGER: 5907192
HOST-RESOURCES-MIB::hrStorageUsed.3 = INTEGER: 6526984
HOST-RESOURCES-MIB::hrStorageUsed.6 = INTEGER: 548284
HOST-RESOURCES-MIB::hrStorageUsed.7 = INTEGER: 1998532
HOST-RESOURCES-MIB::hrStorageUsed.10 = INTEGER: 619792
HOST-RESOURCES-MIB::hrStorageUsed.31 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageUsed.32 = INTEGER: 3089177
HOST-RESOURCES-MIB::hrStorageUsed.33 = INTEGER: 38735
HOST-RESOURCES-MIB::hrStorageUsed.34 = INTEGER: 8858338
HOST-RESOURCES-MIB::hrStorageUsed.35 = INTEGER: 8140095
HOST-RESOURCES-MIB::hrStorageUsed.36 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageUsed.37 = INTEGER: 0
HOST-RESOURCES-MIB::hrStorageUsed.38 = INTEGER: 49018100
```
Ce qui correspond bien à la capture et au résultat de la commande plus haut.

## 5. Avec un logiciel à choix parmis les suivants:

* **snpwalk** (shell) ou **tkmib** (GUI simpliste) sous Unix
* **Open Eyes** sous Java
* **getif** sous Windows
* **[oidview](http://www.oidview.com/mibs/detail.html)** sur le Net

Déterminez, en n'oubliant pas de configurer la destination (nom de l'agent interrogé), ici `157.26.77.13`:

### a. 
Le numéro d'identification de l'entrée pointant sur le nombre d'interfaces réseaux (`iso.org.dod.internet.mgmt.mib-2.interfaces`),puis obtenez les noms de ses interfaces réseau.

Tkmib nous permet de visualiser les branches après `.interface`, leur **OID** en plus d'une descritpion.

* Un `get` de `.1.3.6.1.2.1.2.1` nous retourne le nombre d'interfaces:

```
sol@debian:~$ snmpget -v 1 -c public 157.26.77.13 .1.3.6.1.2.1.2.1.0
IF-MIB::ifNumber.0 = INTEGER: 26
```

cette commande est équivalente à:

```
sol@debian:~$ snmpget -v 1 -c public 157.26.77.13 .iso.org.dod.internet.mgmt.mib-2.interfaces.ifNumber.0
IF-MIB::ifNumber.0 = INTEGER: 26

```

* La description de Tkmib nous dit que l'entrée suivante contient la liste des interfaces:

<a href="/00illustrations/labo6/tkmib.png"><img src="/00illustrations/labo6/tkmib.png"></a>

Nous pouvons visualiser le nom des interfacea dans la gui avec un display sous forme de tableau. 

Nous pouvons égalment faire un `snmpwalk` à la ligne de commande sur le premier élément de cette entrée:  

`sol@debian:~$ snmpwalk -v 1 -c public 157.26.77.13 .1.3.6.1.2.1.2.2`

Et voici l'extrais qui nous interesse:
```
...
IF-MIB::ifDescr.1 = STRING: lo
IF-MIB::ifDescr.2 = STRING: eth0
IF-MIB::ifDescr.3 = STRING: eth1
IF-MIB::ifDescr.4 = STRING: br0
IF-MIB::ifDescr.6 = STRING: veth11
IF-MIB::ifDescr.8 = STRING: veth15
IF-MIB::ifDescr.10 = STRING: veth151
IF-MIB::ifDescr.12 = STRING: veth152
IF-MIB::ifDescr.14 = STRING: veth153
IF-MIB::ifDescr.16 = STRING: veth154
IF-MIB::ifDescr.18 = STRING: veth155
IF-MIB::ifDescr.20 = STRING: veth156
IF-MIB::ifDescr.22 = STRING: veth157
IF-MIB::ifDescr.24 = STRING: veth158
IF-MIB::ifDescr.26 = STRING: veth159
IF-MIB::ifDescr.28 = STRING: veth160
IF-MIB::ifDescr.30 = STRING: veth161
IF-MIB::ifDescr.32 = STRING: veth162
IF-MIB::ifDescr.34 = STRING: veth163
IF-MIB::ifDescr.36 = STRING: veth164
IF-MIB::ifDescr.38 = STRING: veth165
IF-MIB::ifDescr.40 = STRING: veth166
IF-MIB::ifDescr.42 = STRING: veth167
IF-MIB::ifDescr.46 = STRING: veth169
IF-MIB::ifDescr.48 = STRING: veth170
IF-MIB::ifDescr.50 = STRING: veth168
...
```

### b. 
les informations de l'objet `iso.org.dod.internet.mgmt.mib-2.interfaces.ifTable .ifEntry.ifInOctets`

```
sol@debian:~$ snmpwalk -v 1 -c public 157.26.77.13 .iso.org.dod.internet.mgmt.mib-2.interfaces.ifTable.ifEntry.ifInOctets
IF-MIB::ifInOctets.1 = Counter32: 4170454039
IF-MIB::ifInOctets.2 = Counter32: 623131649
IF-MIB::ifInOctets.3 = Counter32: 0
IF-MIB::ifInOctets.4 = Counter32: 2031979771
IF-MIB::ifInOctets.6 = Counter32: 287160632
IF-MIB::ifInOctets.8 = Counter32: 2470231482
IF-MIB::ifInOctets.10 = Counter32: 55688373
IF-MIB::ifInOctets.12 = Counter32: 84105143
IF-MIB::ifInOctets.14 = Counter32: 17146364
IF-MIB::ifInOctets.16 = Counter32: 114408639
IF-MIB::ifInOctets.18 = Counter32: 24760215
IF-MIB::ifInOctets.20 = Counter32: 47147411
IF-MIB::ifInOctets.22 = Counter32: 21354563
IF-MIB::ifInOctets.24 = Counter32: 43460795
IF-MIB::ifInOctets.26 = Counter32: 31137936
IF-MIB::ifInOctets.28 = Counter32: 57591110
IF-MIB::ifInOctets.30 = Counter32: 40998856
IF-MIB::ifInOctets.32 = Counter32: 158066311
IF-MIB::ifInOctets.34 = Counter32: 117731318
IF-MIB::ifInOctets.36 = Counter32: 29735593
IF-MIB::ifInOctets.38 = Counter32: 31914455
IF-MIB::ifInOctets.40 = Counter32: 98663284
IF-MIB::ifInOctets.42 = Counter32: 93196279
IF-MIB::ifInOctets.46 = Counter32: 2715027
IF-MIB::ifInOctets.48 = Counter32: 1525717
IF-MIB::ifInOctets.50 = Counter32: 299054826
```

## 6. Avancé: Experimenter avec SNMP traps

L'outil `snmptrap` (ou `snmpinform`) permet d'envoyer des `traps` à un manager. Le daemon `snmptrapd` permet de les recevoir et les traiter.

### a.

installation de snmptrapd

### b.

[man](https://manpages.debian.org/jessie/snmp/snmptrap.1.en.html)

`snmptrap` is an SNMP application that uses the SNMP TRAP operation to **send notifications to a network manager**. 
One or more object identifiers (OIDs) can be given as arguments on the command line. **A type and a value must accompany each object identifier**. Each variable name is given in the format specified in variables(5) (notation à nombres ou notation avec les noms).

When invoked as `snmpinform`, **or when -Ci is added to the command line flags of snmptrap**, it sends an INFORM-PDU, **expecting a response from the trap receiver**, retransmitting if required. Otherwise it sends an TRAP-PDU or TRAP2-PDU.

**syntaxes:**

```
snmptrap -v 1 [COMMON OPTIONS] AGENT enterprise-oid agent generic-trap specific-trap uptime [OID TYPE VALUE]...

snmptrap -v [2c|3] [COMMON OPTIONS] [-Ci] AGENT uptime trap-oid [OID TYPE VALUE]...

snmpinform -v [2c|3] [COMMON OPTIONS] AGENT uptime trap-oid [OID TYPE VALUE]...
```

### c.
Effectuer les opérations suivantes dans deux shells:

* lancer le `snmptrapd` sans configuration mais en mode debug sudo `snmptrapd -f -Le -C -d -D`

* envoyez une TRAP (les erreurs read\_config\_store open sont normales) en capturant (Wireshark ou tcpdump **sur l’interface lo**)

`snmptrap -v 1 -c public localhost system 1.2.3.4 3 0 iso.org.dod.internet.mgmt.mib-2.interfaces.1 i 1`

<a href="/00illustrations/labo6/trap.png"><img src="/00illustrations/labo6/trap.png"></a>

`snmpinform` attends une réponse du manager alors que `snmptrap` envoie juste une notification.





# Décodage SNMP (ASN.1/BER)

* [tuto]( http://luca.ntop.org/Teaching/Appunti/asn1.html)
* [x.690](https://en.wikipedia.org/wiki/X.690#Encoding_structure)

* [type] + [len data] $$ \Rightarrow $$  1Byte
* [data] $$ \Rightarrow $$ lenght of `len data`
<br>
<br>

|type|10|0x|
|:--:|:--:|:--:|
|End of content|0|0|
|bool|1|1|
|int|2|02|
|bit string|3|03|
|octet string|4|04|
|NULL|5|05|
|Object identifier|6|06|
|sequence and seq of|16|10|
|set and set of|17|11|
|printable string|19|13|
|T61String|20|14|
|IA5String|22|16|
|UTCTime|23|17|
|VisibleString|26|1a|
|Struct|48|30|


<br>
<br>

-----

```
30 2d 02 01 00 04 06 70 75 62 6c 69 63 a1 20 02
04 22 2d 9d d6 02 01 00 02 01 00 30 12 30 10 06
0c 2b 06 01 02 01 2b 0e 01 01 06 01 01 05 00
```

* **type**: 30 $$ \Rightarrow $$ type construit (Sequence aussi ? d'arès ce que je crois comprendre dans votre mail)
* **len**: $$ 0x2d = 45_{10} (+2) $$ 
* **data**: tout le reste
<br>

------

`02 01`

* **type**: 02 $$ \Rightarrow $$ `int`
* **len**: $$ 0x01 (+2) =  1_{10} (+2) $$
* **data**: `00` $$ \Rightarrow 0_{10} $$
<br>

------

`04 06 70 75 62 6C 69 63`

* **type**: 5 $$ \Rightarrow $$ `octet string`
* **len**: $$ 0x06 (+2) = 6_{10} (+2) $$
* **data**: `` $$ \Rightarrow $$ `70 75 62 6C 69 63`

Conversion string $$ \Rightarrow $$  char:

```py
s = "70 75 62 6C 69 63"
l = [int(x,16) for x in s.split()]

for i in l:
    r = r'{10}'
    print("$$ 0x{} = {}_{} = {} $$"\
        .format(i, int(i), r, chr(int(i))))

[print(chr(x), end='') for x in l]
```

<br>

| 0x112 | $$112_{10}$$ | $$p$$ |
| 0x117 | $$117_{10}$$ | $$u$$ |
| 0x98 | $$98_{10}$$ | $$b$$ |
| 0x108 | $$108_{10}$$ | $$l$$ |
| 0x105 | $$105_{10}$$ | $$i$$ |
| 0x99 | $$99_{10}$$ | $$c$$ |

<br>

$$ \Large \Rightarrow  public $$



------

```
a1 20 02
04 22 2d 9d d6 02 01 00 02 01 00 30 12 30 10 06
0c 2b 06 01 02 01 2b 0e 01 01 06 01 01 05 00
```

* **type**: $$ 0xA1 \Rightarrow $$ Sequence
* **len**: $$ 0x20 = 32_{10} (+2) $$
* **data**: tout le reste
<br>


------

`02 04 22 2d 9d d6`

* **type**:  $$0x02 \Rightarrow $$ `int`
* **len**:  $$ 0x04 (+2) = 4 (+2)
* **data**: $$ 0x222d9dd6  \Rightarrow  573414870_{10} $$
<br>

------

`02 01 00`

* **type**: $$ 0x02  \Rightarrow $$ `int`
* **len**: $$0x01 (+2) = 1 (+2) $$
* **data**: $$ 0x00 \Rightarrow 0_{10} $$
<br>

------

`02 01 00`

* **type**: $$ 0x02 \Rightarrow $$ `int`
* **len**: $$0x01 (+2) = 1 (+2) $$
* **data**: $$ 0x00 \Rightarrow 0_{10} $$
<br>

------

`30 12`

* **type**:  $$ 0x30 \Rightarrow $$ type construit
* **len**:  $$ 0x12 (+2) = 18 (+2)
* **data**: tout le reste
<br>

------

`30 10`

* **type**:  $$ 0x30 \Rightarrow $$ type construit
* **len**:  $$ 0x10 (+2) = 16 (+2)
* **data**: tout le reste
<br>

-----

`06 0c 2b 06 01 02 01 2b 0e 01 01 06 01 01`

* **type**:  $$ 0x06 \Rightarrow $$ objet
* **len**:  $$ 0x0c (+2) = 12 (+2)
* **data**: `2b 06 01 02 01 2b 0e 01 01 06 01 01` 

Après avoir regardé quelques trames des captures je reconnais `.6.1.2.1` (...DoD.Internet.management.MIB-2.) mais il manque le début `.1.3.` qui est remplacé par `2b`. Après vérification sur **Tkmib** `.43` n'existe pas mais cela ne veur rien dire, nous somme peut être dans une MIB qui possède ce `.43`. Sans certitude j'ai envie de dire que ce champ veut dire: `1.3.6.1.2.1.43.14.1.1.6.1.1`
<br>

------

`05 00`

* **type**:  $$ 0x05 \Rightarrow $$ NULL
* **len**:  $$ 0x00 (+2) = 0_{10} (+2) $$
* **data**: NULL
<br>

-----

#### Tentative de mise sous forme ASN.1

```
TYPE CONSTRUIT {
    INTEGER {
        version-1{0}
    },

    OCTET STRING {
        community{public}
    },
    SEQUENCE {
        INTEGER {
            573414870  -- ip? (ça peut être une ip dans un autre format?)
        },
        INTEGER {
            0
        },
        INTEGER {
            0
        },
        TYPE CONSTRUIT {
            TYPE CONSTRUIT {
                OBJECT IDENTIFIER {
                    1.3.6.1.2.1.43.14.1.1.6.1.1
                },
                NULL
                }
            }
        }
    }
}
```





# Configuration d'un agent SNMP

* installer un agent (daemon)
* configurer quelques éléments de sécurité basique et autoriser la modification de données

## 1. Installation de snmpd 
ok

## 2. Consulter le fichier /etc/snmp/snmpd.conf
Chmod à faire !

## 3. Adapter des données dans ce fichier et tester avec snmpwalk + questions

```
sol@debian:/etc/snmp$ snmpwalk -v 1 -c public localhost .1.3.6.1.2.1.1
SNMPv2-MIB::sysDescr.0 = STRING: Linux debian 3.16.0-4-amd64 #1 SMP Debian 3.16.43-2 (2017-04-30) x86_64
SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
...
...
```
### a.
Qu'est-ce qu'une communauté ? Comment protéger (un peu) votre agent SNMP?

* Les **communautés**  (v1,2) permettent de filtrer l'accès aux informations d'une **MIB**
    * `public` $$ \Rightarrow $$  lecture seule (default)
    * `private` $$ \Rightarrow $$ donne accès à toute la configuration système en lecture/écriture

> Généralement, lors d’un déploiement de SNMPv1 et SNMPv2, aucune technique de sécurisation de ces protocoles n’est réalisée car aucun mécanisme ne le permet réellement. Alors, la sécurisation en périphérie du protocole doit être réalisée (filtrage à différents niveaux, mécanisme anti-spoofing, …).

[source](https://blog.cedrictemple.net/341-configuration-avancee-de-snmp-sur-linux-snmpv3)

SNMPv3 permet de répondre à ces problèmes.
1. les trames peuvent être chiffrées grâce à différents algorithmes
2. de utilisateurs peuvent être créés, chacun disposant d'un identifiant et d'un mot de passe personnel.




### b.
Activer un nom de communauté permettant de modifier la configuration et modifiez le nom de la machine.

<span style="color:red"> a finir </span> 

# Conception d'une application simple Python

* Faire un petit graph Web de statistiques d'un équipement réseau via SNMP
* Librairies nécéssaires
    * `PySNMP`
    * `http.server`
    * `matPlotLib`

L'application interrogera un équipement par protocole **SNMP** et répondra sur le port 80 en **HTTP**. L'application supportera une seule fonction qui retourne un document au format **MIME** (image/svg+xml) qui sera affichée par le navigateur.

## Délivrable

### Architecture de l'application


<a href="/00illustrations/labo6/diag2.png"><img src="/00illustrations/labo6/diag2.png"></a>
<br>

### Diagrammes d'interaction avec le client **HTTP** et l'équipement **SNMP**

<br>
<a href="/00illustrations/labo6/diag3.png"><img src="/00illustrations/labo6/diag3.png"></a>

# Mise en place de Docker

<a href="/00illustrations/labo6/docker.png"><img src="/00illustrations/labo6/docker.png"></a>
<br>
<br>
<a href="/00illustrations/labo6/docker2.png"><img src="/00illustrations/labo6/docker2.png"></a>


# Codage et test de la solution (Programme Python)

Notre programme est composé de 3 fichiers.

## dataGraph.py 

 * `getData()` $$ \Rightarrow $$ pull l'**OID** `1.3.6.1.2.1.25.1.6` de l'agent **SNMP** sur la machine `157.26.77.13`

* `graphPlot()` $$ \Rightarrow $$ traite le fichier `data.txt` et retourne un plot dans un fichier `svg`

```py
from pysnmp.smi.view import MibViewController
from pysnmp.hlapi    import *

import matplotlib.pyplot as plt


def getData():
	data = ObjectType(
				ObjectIdentity('1.3.6.1.2.1.25.1.6')
			)

	g = nextCmd (
			SnmpEngine(),
			CommunityData('public', mpModel=0),
			UdpTransportTarget(('157.26.77.13', 161)),
			ContextData(),
			data
		)
	
	errorIndication, errorStatus, errorIndex, varBinds = next(g)
	return int(varBinds[0][1])


def graphPlot():
	file = open('data.txt', 'r')
	tab = [line.strip().split(' ') for line in file]

	fig, ax = plt.subplots(figsize=(12,6), dpi=180)
	plt.hold(False)

	ax.plot( 
		[x[0] for x in tab],
		[y[1] for y in tab],
		'r', 
		label=r'$ running \; process \; ({}) $'\
			.format(tab[len(tab)-1][1])
	)

	ax.legend(loc=2)
	ax.set_xlabel(r'$Time (t)$', fontsize=18)
	ax.set_ylabel(r'$Active Process$', fontsize=18)
	ax.set_title('activeProcess(t)');

	fig.savefig('graph.svg')
	plt.close('all')
	file.close()

```

## httpServ.py 
Partie réseau du programme

```py
import http.server  as hs
import socketserver as ss
import threading


class MyHttpServer(hs.BaseHTTPRequestHandler):
	def do_GET(self):
		self.send_response(200)
		filedir = ""

		if (self.path=="/graph"):
			self.send_header('Content-type','text-html')
			filedir = "graph.html"

		elif (self.path=="/graph.svg"):
			self.send_header('Content-type','image/svg+xml')
			filedir = "graph.svg"
		else:
			self.send_header('Content-type','image/svg+xml')
			filedir = "error.svg"

		self.end_headers()
		f = open(filedir)

		while(f == False):
			time.sleep(0.1)
			f = open(filedir)

		self.wfile.write(bytes(f.read(), 'UTF-8'))
		f.close()


class ThreadedHTTPServer(ss.ThreadingMixIn, 
                         hs.HTTPServer):
	"""#Start HTTPServer in a thread"""
```

## soft.py
 le "main" où sont importé les deux autres fichiers (modules) et est lancé le serveur

```py
import time, threading
import httpServ  as hs
import dataGraph as dg


def closing(server, file):
	file.close()
	server.shutdown()



PORT = 8000
f = open('data.txt', 'w')
f.close()

try:
	serv = hs.ThreadedHTTPServer(
				('localhost', PORT), 
				hs.MyHttpServer
			)

	print("Serving at port", PORT)
	
	serverThread = threading.\
		Thread(target=serv.serve_forever)

	serverThread.start()

	count = 0
	while(1):
		file = open('data.txt', 'a')
		file.write("{} {}{}".\
			format(count, dg.getData(), '\n'))
			
		file.close()
		dg.graphPlot()
		count += 1
		time.sleep(1)

except KeyboardInterrupt:
	closing(serv,f)

closing(serv,f)
```




