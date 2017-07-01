---
layout: post
title: "Reseau: PySNMP"
subtitle: "Tuto PySNMP"
date: 2017-07-01
author: Sol
category: Reseau
tags: reseau 
finished: false
---

## Sources
[Mkina-corpus partie 2](https://makina-corpus.com/blog/metier/2016/initiation-a-snmp-avec-python-pysnmp-partie2)  
[wikipedia](https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol)
[pySnmp](http://pysnmp.sourceforge.net/docs/snmp-design.html)

## Liens internes 
[SNMP]()

## 1. Introduction

`PySNMP` est une librairie **Python** qui implémente toutes les versions du protocole `SNMP` et ce entièrement en **Python** (Pas de C).

Ses principles caractéristiques sont:

* Complète, elle implémente l'intégralité du protocole SNMP (v1 à v3)
* Légère, environs 15000 lignes de code
* Assez simple pour les choses simples (phrase de merde)
* Très utilisée 
* Très pratique pour écrire des sondes nagios/centreon/autre ou récupérer des informations `SNMP` dans nos programmes **Python**
* Permet d'écrire nos propres agents
* Parfois complexe et manque d'une documentation claire sur certains éléments comme les agents

### Installation

Linux/Windows:

```
pip install pysnmp
```

### Manipuler les données ASN.1
Il nous arriver de manipuler des données au format **ASN.1** puisque c'est celui qui est utilisé par le protocole `SNMP`. 

La librairie `PySNMP` s'appuie sur une utre librairie: `pyasn1` pour réaliser cela. La conversion des types **ASN.1** vers python est presque naturelle:

```py
>>> from pyasn1.type.univ import *
>>> a = Integer(21) * 2
>>> print(a)
42
>>> a = Integer(-1) + Integer(1)
>>> print(a)
0
>>> a = int(Integer(42))
>>> print(a)
42
>>> a = OctetString('Hello') + ', ' + OctetString(hexValue='5079534e4d5021')
>>> print(a, '\n', type(a))
Hello, PySNMP! 
 <class 'pyasn1.type.univ.OctetString'>
>>> 
```

### Commandes de base

`PySNMP` propose différentes fonctions de **bas niveau** pour utiliser le protocole SNMP, mais il est conseillé d'utiliser le module `hlapi` signifiant **High Level API**.

Ce dernier propose des fonctions de haut niveau assez faciles à paramétrer pour exécuter les commandes de base que l'on peut lister de cette manière:

```py
from pysnmp.hlapi import *
l = [ x for x in dir() if 'Cmd' in x]
print(l)
help('pysnmp.hlapi.getCmd')
```

output:

```py
['bulkCmd', 'getCmd', 'nextCmd', 'setCmd']
Help on function getCmd in pysnmp.hlapi:

pysnmp.hlapi.getCmd = getCmd(snmpEngine, authData, transportTarget, contextData, *varBinds, **options)
    Creates a generator to perform one or more SNMP GET queries.

...
```

Avant d'utiliser ces commandes nous allons apprendre à manipuler les paramètrer dont elles ont besoin:
* Les `OIDs` qu'on souhaite consulter/modifier
* Les éléments de connexion `communauté` pour le protocole v2 ou l'utilisateur pour le protocole v3

### Les OIDS

Les `OIDs` sont les identidiants des informations décrites dans les `MIBs`. Un `OID` indique le chemin à suivre dans l'arbre de la `MIB` pour trouver l'information

`1.2.3.0` pourrait se comprendre par:

* `.` en partant de la place centrle, plusieurs rues sont face à vous
* `1` prenez la première à partir de la guche, vous arrivez sur une nouvelle place.
* `2` plusieurs rues sont de nouveau face  vous. Prenez la seconde sur votre gauche. Vous arrivez sur une nouvelle place
* `3` plusieurs rues sont de nouveau face  vous. Prenez la troisième sur votre gauche. Vous arrivez sur une nouvelle place
* `0` il y a une plaque sur la place, elle contient votre information. Si vous avez envoyé un `GET` vous la lisez, si vous avez envoyé un `SET` vous la modifiez

![alt](https://makina-corpus.com/blog/metier/2016/pysnmp/PySNMP1.png)

Un `OID` est représenté soit par une suite de valeurs numériques, soit par une suite de noms désignant chaque branche parcourue dans la `MIB`. 

Pour créér un `OID` avec `PySNMP` il convient d'instancier la classe `ObjectIdentity`.

Un `OID` peut être défini par:
* une string
* un tuple
* son libellé

Les 4 instances suivantes définissent le même objet

```py
from pysnmp.hlapi import *

o = ObjectIdentity('1.3.6.1.2.1.1.3.0')
o = ObjectIdentity((1,3,6,1,2,1,1,3,0))
o = ObjectIdentity('SNMPv2-MIB', 'sysUpTime', 0)
o = ObjectIdentity('iso.org.dod.internet.mgmt.mib-2.system.sysUpTime.0')
```

Cela semble simple mais à l'utilisation les soucis commencent:

```py
from pysnmp.hlapi import *

o = ObjectIdentity('1.3.6.1.2.1.1.3.0')

print(o.getLabel())
print(o.getMibNode())
print(o.getMibSymbol())
print(o.getOid())
print(o.prettyPrint())
```

output:

```py
Traceback (most recent call last):
  File "c:\Users\sol.rosca\Cloud\Cours\Programmation\Python-Snmp\00\00.py", line 5, in <module>
    print(o.getLabel())
  File "C:\Python3\lib\site-packages\pysnmp\smi\rfc1902.py", line 186, in getLabel
    raise SmiError('%s object not fully initialized' % self.__class__.__name__)
pysnmp.smi.error.SmiError: ObjectIdentity object not fully initialized
```
Il faut effectivement rechercher cet `OID` dans la `MIB`, ce n'est pas automatique car il y a de nombreuses `MIBs`. `PySNMP` est fourni avec tout un arsenal de `Mibs`, néanmoins il faut lui dire où les trouver.

La commande `help` de **Python** montre qu'il existe une méthode `resolveWithMib`. Cette méthode prend en paramètre un objet `MibViewController`. Ce contrôleur a pour rôle de résoudre la correspondance `OID`/`MIB`.

La doc de la classe `resolveWithMib` est quasiment inexistante. Cette classe prend un objet `MibBuilder` en argument lors de sa création. Il faut donc aller voir la classe `MibBuilder`

...mais une petite recherche dans l'arborescence de `PySNMP` montre qu'apparament un `MibViewController` s'obtient à partir du monteur `SNMP` engin. L'engin est en quelque sorte le coeur de la librairie `PySNMP` qui nous permet de dialoguer avec les différents éléments du protocole (messages, hôtes, communautés, `OIDs`, `MIBs`, etc...)

On peut avoir plusieurs moteurs pour, par exemple travailler avec plusieurs `MIBs`. Notre code devient alors:

```py
from pysnmp.hlapi import *

se = SnmpEngine()
mvc = se.getUserContext('mibViewController')
o = ObjectIdentity('1.3.6.1.2.1.1.3.0')

o.resolveWithMib(mvc)

print(o.getLabel())
print(o.getMibNode())
print(o.getMibSymbol())
print(o.getOid())
print(o.prettyPrint())
```

output:

```py
Traceback (most recent call last):
  File "c:\Users\sol.rosca\Cloud\Cours\Programmation\Python-Snmp\00\00.py", line 7, in <module>
    o.resolveWithMib(mvc)
  File "C:\Python3\lib\site-packages\pysnmp\smi\rfc1902.py", line 356, in resolveWithMib
    addMibCompiler(mibViewController.mibBuilder,
AttributeError: 'NoneType' object has no attribute 'mibBuilder'
```
Toujours pas...

Le `mibViewController` n'est pas défini car nous n'avons pas créé de `UserContext`. La solution la plus simple revient, finalement, à créer nous-même le `MibViewController`:

```py
from pysnmp.hlapi import *
from pysnmp.smi.view import MibViewController

se = SnmpEngine()
mvc = se.getUserContext('mibViewController')

if not mvc:
    mvc = MibViewController(se.getMibBuilder())

o = ObjectIdentity('1.3.6.1.2.1.1.3.0')
o.resolveWithMib(mvc)

print(o.getLabel())
print(o.getMibNode())
print(o.getMibSymbol())
print(o.getOid())
print(o.prettyPrint())
```

output:

```
('iso', 'org', 'dod', 'internet', 'mgmt', 'mib-2', 'system', 'sysUpTime')
MibScalar((1, 3, 6, 1, 2, 1, 1, 3), TimeTicks())
('SNMPv2-MIB', 'sysUpTime', (ObjectName('0'),))
1.3.6.1.2.1.1.3.0
SNMPv2-MIB::sysUpTime.0
```

Nous y voila. Le contrôleur de `MIB` utilise le `MIB` builder oar défaut de PySNMP qui stocke les versions "pythonisées" des `MIBS` dans le sous-dossier d'installation de `PySNMP`: `pysnmp/smi/mibs`. <span style="color:gree"> ne pas hésiter à y jetter un oeil </span> .

