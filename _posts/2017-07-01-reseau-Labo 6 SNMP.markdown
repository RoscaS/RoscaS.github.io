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

## 1 Premiers pas avec SNMP

### Objectifs
* interroger un agent `SNMP` avec des outils `UNIX` (modèle PULL **client-serveur** classique)
* concepts de base de la **MIB**
* modèle `PUSH`: les traps


### question 3

**snmpget**

```
sol@debian:~$ snmpget -v 1 -c public 157.26.77.13 system.sysName.0
SNMPv2-MIB::sysName.0 = STRING: gandalf
```

<a href="/00illustrations/labo6/wireshark1.png"><img src="/00illustrations/labo6/wireshark1.png"></a>

Nous voyons ici que l'information system.sysname.0 est répertoriée par l'**OID** `1.3.6.1.2.1.1.5.0` qui est équivalente à `.ISO.organization.DoD.Internet.management.MIB-2.system.sysName.0` en suivant l'arbre sur l'illustration suivante:

![alt](https://makina-corpus.com/blog/metier/2016/pysnmp/snmp_mib.png)


**snmpwalk**

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


**snmpdf**

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

Il est interessant de voir que dans un premier temps les requètes visent la description de chaque ligne et une fois les descriptions acquises les valeurs completant les colones **size**, **used**, **available** et **used%** dont demandées en une fois et reçues en une fois. (commande `getbulk`?)



