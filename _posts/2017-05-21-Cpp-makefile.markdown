---
layout: post
title: "Cpp: makefile (fr)"
subtitle: "Makefile tuto"
date: 2017-05-22
author: Sol
category: Cpp
tags:  Cpp res make tuto fr
finished: false
---

[developpez.com](http://gl.developpez.com/tutoriel/outil/makefile/)

[learn X in Y](https://learnxinyminutes.com/docs/fr-fr/make-fr/)


## Introduction

Les Makefiles sont des fichiers, généralement appelés makefile ou Makefile, utilisés par le programme make pour exécuter un ensemble d'actions, comme la compilation d'un projet, l'archivage de document, la mise à jour de site, etc. Cet article présentera le fonctionnement de makefile au travers de la compilation d'un petit projet en C.

Il existe une multitude d'utilitaires de Makefile fonctionnant sur différents systèmes (gmake, nmake, tmake, etc.). Les Makefiles n'étant malheureusement pas normalisés, certaines syntaxes ou fonctionnalités peuvent ne pas fonctionner sur certains utilitaires.
Le présent article se base sur l'utilitaire GNU make. Toutefois les notions abordées devraient être utilisables avec la majorité des utilitaires.

## 1. Projet: Hello world

Le projet utilisé ici sera un classique "hello world" découpé en trois fichiers:

```c
// hello.c
#include<stdio.h>
#include<stdlib.h>

void hello(void)
{
    printf("Hello World\n");
}
```

```c
// hello.h
#ifndef H_GL_HELLO
#define H_GL_HELLO

void hello(void);

#endif
```

```c
// main.c
#include<stdio.h>
#include<stdlib.h>
#include"hello.h"

int main(void)
{
    Hello();
    return EXIT_SUCCES;
}

```

## 2. Présentation de Makefile

Un Makefile est un fichier constitué de plusieurs règles de la forme:

```makefile
cible: dependance
    commandes
```

Chaque commande est précédée d'une tabulation. Lors de l'utilisation d'un tel fichier via la commande make, la première règle rencontrée, ou la règle dont le nom est spécifié, est évaluée. L'évaluation d'une règle se fait en plusieurs étapes:

* les dépendances sont analysées, si une dépendance est la cible d;une autre règle du Makefile, cette regle est à son tour évaluée.
* Lorsque l'ensemble des dépendances sont analysées et si la cible ne correspond pas à un fichier existant ou si un fichier dépendance est plus récent que la regle, les différentes commandes sont exécutées.

#### 2.1 Makefile minimal

Le Makefile minimal du projet est donc:

```makefile
# Makefile

hello: hello.o main.o
    gcc -o hello hello.o main.o

hello.o: hello.c
    gcc -o hello.o -c hello.c -W -Wall -ansi -pedantic

main.o: main.c hello.h
    gcc -o main.o -c main.c -W -Wall -ansi -pedantic
```

Nous cherchons à créer le fichier executable `hello`, la première dépendance est la cible d'une des régles de notre Makefile, nous évaluons donc cette règle.

Comme aucune dépendance de hello.o n'est une règl, aucune règle n'est à évaluer pour compléter celle-ci.

Deux cas se présentent ici: 
* soit le fichier hello.c est plus récent que le fichier hello.o, la commande est alors exécutée et hello.o est construit 
* soit hello.o est plus récent que hello.c et la commande n'est pas executée.

L'évaluation de la règle hello.o est terminée. Les autres dépendances de hello sont examinées de la même manière puis, si nécéssaire la commande de la règle hello est exécutée et hello est construit.


## 3. CFLAGS

`CFLAGS` et `CXXFLAGS` sont les noms de variables d'environnement ou de variables du Makefile qui peuvent être utilisées pour paramétrer la compilation d'un logiciel.

Ces variables sont habituellement positionnées dans un Makefile et sont ajoutées quand le compilateur est appelé. Si elles ne sont pas spécifiées dans le Makefile, alors elles seront prises directement à partir de l'environnement, si elles sont présentes.

`CFLAGS` permet d'ajouter des paramètres sur la ligne de commande qui appelle le compilateur C, alors que `CXXFLAGS` est utilisé pour la compilation **C++**. De même, une variable similaire, `CPPFLAGS`, permet de passer des paramètres **sur la ligne de commande du Préprocesseur C**.

* **-Wall : -W represents warnings. -Wall means it will show all possible warnings.**

* -O2 : means optimize it. These are used in creating machine code from source code by compiler. There are many levels of optimizing. O2 is generally considered good enough, while O3 makes your file size bigger without optimizing significantly, and may make your program slower on some sort of algorithms.

* -g means debug. This will add many debugging "notes" in your final binary, so that when you run a debugger on your final binary file, the debugger will know what part of this binary file is related to which part of the original source code.

* -I/path/to/include/files : In C/C++, you can include files which compiler already knows where they are present with command: #include <filename.h>. If the compiler does not know where the include file you want to include is present, you can specify the path to that directory with -I/the/directory



## 4. Learn X in Y. X = Make

```makefile
# Ceci est un commentaire.

# Un makefile devrait être nommé "Makefile" (avec ou sans la
# majuscule). Il peut alors être exécuté par `make <cible>`.
# Ce nommage n'est toutefois pas obligatoire : utiliser
# `make -f "fichier" <cible>`.

# ATTENTION : l'indentation est quant à elle obligatoire, et se fait avec des
# tabulations, pas avec des espaces !

#-----------------------------------------------------------------------
# Les basiques
#-----------------------------------------------------------------------

# Une règle. Elle ne sera exécutée que si fichier0.txt n'existe pas.
fichier0.txt:
    echo "truc" > fichier0.txt
    # Même les commentaires sont transférés dans le terminal.

# Cette règle ne sera exécutée que si fichier0.txt est plus récent que
# fichier1.txt.
fichier1.txt: fichier0.txt
    cat fichier0.txt > fichier1.txt
    # Utiliser la même syntaxe que dans un terminal.
    @cat fichier0.txt >> fichier1.txt
    # @ empêche l'affichage de la sortie texte d'une commande.
    -@echo 'hello'
    # - signifie que la règle devrait continuer à s'exécuter si cette commande
    # échoue.

# Une règle peut avoir plusieurs cibles et plusieurs dépendances.
fichier2.txt fichier3.txt: fichier0.txt fichier1.txt
    touch fichier2.txt
    touch fichier3.txt

# Make affichera un avertissement si le makefile comporte plusieurs règles pour
# une même cible. Cependant les règles vides ne comptent pas, et peuvent être
# utilisées pour ajouter des dépendances plus facilement.

#-----------------------------------------------------------------------
# Fausses règles
#-----------------------------------------------------------------------

# Une fausse règle est une règle qui ne correspond pas à un fichier.
# Par définition, elle ne peut pas être à jour, et donc make l’exécutera à
# chaque demande.
all: maker process

# La déclaration des règles peut être faite dans n'importe quel ordre.
maker:
    touch ex0.txt ex1.txt

# On peut transformer une règle en fausse règle grâce à la cible spéciale
# suivante :
.PHONY: all maker process

# Une règle dépendante d'une fausse règle sera toujours exécutée.
ex0.txt ex1.txt: maker

# Voici quelques exemples fréquents de fausses règles : all, make, clean,
# install...

#-----------------------------------------------------------------------
# Variables automatiques et wildcards
#-----------------------------------------------------------------------

# Utilise un wildcard pour des noms de fichier
process: fichier*.txt
    @echo $^    # $^ est une variable contenant la liste des dépendances de la
                # cible actuelle.
    @echo $@    # $@ est le nom de la cible actuelle. En cas de cibles
                # multiples, $@ est le nom de la cible ayant causé l'exécution
                # de cette règle.
    @echo $<    # $< contient la première dépendance.
    @echo $?    # $? contient la liste des dépendances qui ne sont pas à jour.
    @echo $+    # $+ contient la liste des dépendances avec d'éventuels
                # duplicatas, contrairement à $^.
    @echo $|    # $| contient la liste des cibles ayant préséance sur la cible
                # actuelle.

# Même si la définition de la règle est scindée en plusieurs morceaux, $^
# listera toutes les dépendances indiquées.
process: ex1.txt fichier0.txt
# Ici, fichier0.txt est un duplicata dans $+.

#-----------------------------------------------------------------------
# Pattern matching
#-----------------------------------------------------------------------

# En utilisant le pattern matching, on peut par exemple créer des règles pour
# convertir les fichiers d'un certain format dans un autre.
%.png: %.svg
    inkscape --export-png $^

# Make exécute une règle même si le fichier correspondant est situé dans un sous
# dossier. En cas de conflit, la règle avec la meilleure correspondance est
# choisie.
small/%.png: %.svg
    inkscape --export-png --export-dpi 30 $^

# Dans ce type de conflit (même cible, même dépendances), make exécutera la
# dernière règle déclarée...
%.png: %.svg
    @echo cette règle est choisie

# Dans ce type de conflit (même cible mais pas les mêmes dépendances), make
# exécutera la première règle pouvant être exécutée.
%.png: %.ps
    @echo cette règle n\'est pas choisie si *.svg et *.ps sont présents

# Make a des règles pré établies. Par exemple, il sait comment créer la cible
# *.o à partir de *.c.

# Les makefiles plus vieux utilisent un matching par extension de fichier.
.png.ps:
    @echo cette règle est similaire à une règle par pattern matching

# Utiliser cette règle spéciale pour déclarer une règle comme ayant un
# matching par extension de fichier.
.SUFFIXES: .png

#-----------------------------------------------------------------------
# Variables, ou macros
#-----------------------------------------------------------------------

# Les variables sont des chaînes de caractères.

variable = Ted
variable2="Sarah"

echo:
    @echo $(variable)
    @echo ${variable2}
    @echo $variable    # Cette syntaxe signifie $(n)ame et non pas $(variable) !
    @echo $(variable3) # Les variables non déclarées valent "" (chaîne vide).

# Les variables sont déclarées de 4 manières, de la plus grande priorité à la
# plus faible :
# 1 : dans la ligne de commande qui invoque make,
# 2 : dans le makefile,
# 3 : dans les variables d’environnement du terminal qui invoque make,
# 4 : les variables prédéfinies.

# Assigne la variable si une variable d’environnement du même nom n'existe pas
# déjà.
variable4 ?= Jean

# Empêche cette variable d'être modifiée par la ligne de commande.
override variable5 = David

# Concatène à une variable (avec un espace avant).
variable4 +=gris

# Assignations de variable pour les règles correspondant à un pattern
# (spécifique à GNU make).
*.png: variable2 = Sara # Pour toutes les règles correspondant à *.png, et tous
                        # leurs descendants, la variable variable2 vaudra
                        # "Sara".
# Si le jeux des dépendances et descendances devient vraiment trop compliqué,
# des incohérences peuvent survenir.

# Certaines variables sont prédéfinies par make :
affiche_predefinies:
    echo $(CC)
    echo ${CXX}
    echo $(FC)
    echo ${CFLAGS}
    echo $(CPPFLAGS)
    echo ${CXXFLAGS}
    echo $(LDFLAGS)
    echo ${LDLIBS}

#-----------------------------------------------------------------------
# Variables : le retour
#-----------------------------------------------------------------------

# Les variables sont évaluées à chaque instance, ce qui peut être coûteux en
# calculs. Pour parer à ce problème, il existe dans GNU make une seconde
# manière d'assigner des variables pour qu'elles ne soient évaluées qu'une seule
# fois seulement.

var := A B C
var2 ::=  $(var) D E F # := et ::= sont équivalents.

# Ces variables sont évaluées procéduralement (i.e. dans leur ordre
# d'apparition), contrairement aux règles par exemple !

# Ceci ne fonctionne pas.
var3 ::= $(var4) et fais de beaux rêves
var4 ::= bonne nuit

#-----------------------------------------------------------------------
# Fonctions
#-----------------------------------------------------------------------

# Make a une multitude de fonctions. La syntaxe générale est
# $(fonction arg0,arg1,arg2...).

# Quelques exemples :

fichiers_source = $(wildcard *.c */*.c)
fichiers_objet  = $(patsubst %.c,%.o,$(fichiers_source))

ls: * src/*
    @echo $(filter %.txt, $^)
    @echo $(notdir $^)
    @echo $(join $(dir $^),$(notdir $^))

#-----------------------------------------------------------------------
# Directives
#-----------------------------------------------------------------------

# Inclut d'autres makefiles.
include meuh.mk

# Branchements conditionnels.
sport = tennis
report:
ifeq ($(sport),tennis)                  # Il y a aussi ifneq.
    @echo 'jeu, set et match'
else
    @echo "C'est pas ici Wimbledon ?"
endif

truc = true
ifdef $(truc)                           # Il y a aussi ifndef.
    machin = 'salut'
endif
```