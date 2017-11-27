---
layout: post
title: "SQL: 2 Base de données relationnelle"
subtitle: ""
date: 2017-11-26
author: Sol
category: SQL
tags: 
finished: false
mathjax: true
---

# Base de données relationnelle

Façon d'optimiser l'organisation d'une base de donnée pour:
* Maximiser l'espace disponible
* Réduire la redondance des données
* Empeche les anomalies de MAJ
* Booster la performance des query


# Normalisation de base de donnée

C'est l'action d'organiser une base de données de façon relationnelle.

L'organisation d'une base de données en **tables** et **colonnes** s'appelle **normalisation**. L'idée principale est qu'une table ne contient que des données concernant un sujet unique. La normlisation tente aussi de mettre en commun les données appartenant à plusieurs tables.


# Définitions

* **Constraints** (Contraintes): rêgle que la base de donnée doit suivre. Typiquement les Keys sont des contraintes appliquées sur des colonnes. Les 3 Key qui suivent sont des contraintes.

## Primary Key
Utilisée pour identifier un enregistrement (ligne) dans une table. Cette clé sert aussi à référencer son enregistrement dans d'autres tables ou dans le logiciel qui utilise la base de données.

* Doit être unique
* Ne peut pas valoir 0 (les BDD concidèrent que $0\neq0$ )
* Une table ne peut en comporter qu'une seule
* Toutes les tables **devraient** en avoir une

## Unique Key

Assure que des données ne sont pas dupliquées dans deux enregistrements distincts d'une même table. Autrement dit, les champs (colonne) qui ont une contrainte d'Unique key, doivent contenir des valeurs différentes. **Une Primary key est donc par définition Unique key aussi**. Mais comme il ne peut y avoir qu'une Primary key, si on veut assurer que tous les champs d'une colonne en particulier sont tous différentes, on peut appliquer plusieurs contrainte Unique Key dans la même table.

> Il est bon de garder en tête que par défaut, de nombreuses BDD indexent et ordonnent physiquement les tables sur le disque en utilisant les valeurs de la Primary Key. Cela veut dire que query la colonne qui possède la contrainte Primary key est plus performant que de query une autre colonne.

## Différences entre PK et UK

* **Comportement**: PK est utilisée pour identifier un enregistrement (ligne) dans une table alors qu'une UK sert a empécher les doublons dans les champs d'une colonne.

* **Valeur Null**: Les PK ne peuvent pas être nulles alors que les UK peuvent.

* **Existance**: Une table peut au maximum avoir une PK mais peut avoir plusieurs UK.

* **Modifiable**: On ne peut changer ou delete la valeur d'une PK contrairement aux valeurs d'une UK.

## Foreign Key

On peut voir la FK comme un pointeur ou une référence d'une table vers une autre table. C'est cette contrainte qui définit la relation entre deux tables et fait la particularité des BDD Relationnelles.

<img src="/00illustrations/RDB/fk1.png" align="" height="size">

> Dans cette illustration, les valeurs de la collonne `ProductID` dans la table **Sale** référencent des enregistrements dans la colonne `ProductID` dans la table **Products**.

<img src="/00illustrations/RDB/fk2.png" align="" height="size">

> Tous les enregistrement dont la valeur de la colonne `ProductID` vaut `1` dans **Sale** référencent le même enregistrement dans la table **Products**


**La colonne `ProductID` dans la table Sale est une Foreign Key**
**La colonne `ProductID` dans la table Products est une Primary Key**

## Referential integrity (Aspect primordial des BDDR): 

Cela veut dire que La Foreign Keys doit exister en tant que Primary Key dans la table que la Foreign key référence. Cela veut dire que sans FK Constraints, notre BDD manque d'intégrité.

# Relations entre tables.

Il existe 3 types de relations entre tables d'une BDD:

1. One to one
2. One to many
3. Many to many

## One to many
C'est la relation la plus courante. Ce type de relation veut dire qu'un enregistrement dans une table peut référencer de nombreuses autres dans la seconde table (mais pas le contraire!).
* **one** est contenu dans la table qui contient la **PK** de la relation
* **many** sont contenu dans la table qui contient la **FK** de la relation

<img src="/00illustrations/RDB/oneToMany01.png" align="" height="size">

> Dans cette illustration, la table **Product** a une collonne `ProductID` a qui est appliqué une contrainte **PK**. Les champs de cette collonne sont chacuns référencés par plusieurs enregistrements dans la table **Sales** via les champs de la collonne `ProductID`.

## One to One
Bien moins courante, cette relation est principalement utilisée pour étendre une table qui n'est pas éditable pour une raison ou pour une autre:

<img src="/00illustrations/RDB/oneOne01.png" align="" height="size">

> Dans cette illustration, nous constatons que les champs des deux colonnes `ID` n'apparaissent qu'une fois dans chaque table. Si nous appliquons une contrainte PK à la colonne `ID` de **Customer_Extension** et que nous la lions via une contrainte FK à la colonne `ID` de la table **Customer**, ces deux tables se comporteront comme une unique table qui est la fusion des deux.

## Many to Many

N'existe pas en tant que tel, c'est 2 relations One to many qui via une 3e table dite "de jonction" relie 2 autres tables.

<img src="/00illustrations/RDB/manyMany01.png" align="" height="size">

> La table `Order` est composée de plusieurs enregistrements de la table `Part` et un enregistrement de la table `Part` peut être dans plusieurs enregistrements de la table `Order`. Il est difficile de lier ces deux tables du a la difficulté de choisir quelle table contiendra la FK sans créer de doublons.

<img src="/00illustrations/RDB/manyMany02.png" align="" height="size">

> La création d'une table de jonction qui contient les `PartID` de chaque table nous permet de résoudre ce problème en créant deux relations One to one. On note que ce cas est une illustration d'un PK multi-colonnes.