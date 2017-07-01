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
>>> a = Integer(21)*2
>>> print(a)
42
>>> a = Integer(-1)+Integer(1)
>>> print(a)
0
>>> a = int(Integer(42))
>>> print(a)
42
>>> a = OctetString('Hello')+', '+OctetString(hexValue='5079534e4d5021')
>>> print(a, '\n', type(a))
Hello, PySNMP! 
 <class 'pyasn1.type.univ.OctetString'>
>>> 
```




