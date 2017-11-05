---
layout: post
title: "Divers: Définitions (en-fr)"
subtitle: "définitions diverses"
date: 2017-05-26
author: Sol
category: Divers
tags: divers, fr, en
finished: false
mathjax: true
---

# Divers

### Philanthropie 
(du grec ancien φίλος / phílos « amoureux » et ἄνθρωπος / ánthrôpos « homme », « genre humain ») **est la philosophie ou doctrine de vie qui met l'humanité au premier plan de ses priorités**. Un philanthrope cherche à améliorer le sort de ses semblables par de multiples moyens.

### Tautologie
(du grec ταὐτολογία, composé de ταὐτό, « la même chose », et λέγω, « dire » : le fait de redire la même chose) est une phrase ou un effet de style ainsi tourné que sa formulation ne puisse être que vraie. La tautologie est apparentée au truisme (ou lapalissade) et au pléonasme. 

En **logique mathématique**, le mot « tautologie » désigne une proposition toujours vraie selon les règles du calcul propositionnel. On utilise aussi l'adjectif tautologique en mathématiques pour désigner des structures qui émergent naturellement de la définition de certains objets.

En **logique**, une tautologie est une formule valide pour toutes les assignations possibles et appartient au métalangage.

### Non-free
Contraire d'open-source. Pas forcément payant!

### UTC time
Universal Coordinated Time. "Temps global": Echelle de temps adoptée comme base du temps civil international par la majorité des pays du globe. UTC est une échelle de temps comprise entre le temps atomoique international (**TAI**) qui est stable mais déconnectée de la rotation de la terre et le temps universel (**UT**),  à la rotation de la Terre et donc lentement variable.

Il y a un besoin prgmatique pour un temps global qui aide à éviter la confusion entre fuseaux horaires et heures d'été et d'hiver. UTC est le standard qui répond à ce besoin.

---------------------------------------------------------------------------

# IA (types)

## Sources:
[oezratty.net](http://www.oezratty.net/wordpress/2016/avancees-intelligence-artificielle-1/) (fr) impressionnant !

## intelligence symbolique 
Résout les problèmes avec de la connaissance (c’est le domaine actuel de l’IA et du machine learning).


## intelligence computationnelle
Résout des problèmes avec des données issues d’exemples et de l’apprentissage. Ce dernier domaine intègre notamment les réseaux neuronaux, la logique floue et les algorithmes génétiques.


## le symbolisme
Se focalise sur la pensée abstraite, 

## le connectionisme  
se focalise sur la perception, dont la vision, la reconnaissance des formes et qui s’appuie notamment sur les réseaux neuronaux artificiels 

## le comportementalisme 
s’intéresse aux pensées subjectives de la perception. C’est dans ce  domaine que l’on peut intégrer: 
    * l’informatique affective (affective computing) qui étudie les moyens de reconnaitre, exprimer, synthétiser et modéliser les émotions humaines. C’est une capacité  qu’IBM Watson est censé apporter au robot Pepper d’Aldebaran / Softbank.

> Automatisation des processus cognitifs et en s’appuyant sur quatre étapes : l’observation des faits et données, leur interprétation, leur évaluation et la décision, avec une action ou une proposition d’actions, souvent basée sur des statistiques.



---------------------------------------------------------------------------



# IT

## Langage à typage statique/dynamique

[stackoverflow](https://stackoverflow.com/questions/1517582/what-is-the-difference-between-statically-typed-and-dynamically-typed-languages)

### Typage statique
Un langage est à **typage statique** si le type d'une variable est connu au moment de la compilation (compile-time par opposition a run-time). Dans la majorité des langages à typage statique, cela veut dire que le programmeur doit spécifier le type de chaque variable (C, C++ "classique", Java); d'autre langages à typage statique offre un certain niveau de reconnaissance de type (comme la fonction `auto` du C++ "moderne") qui permet au langage de déduire le type d'une variable (OCaml, Haskell, Scala, Kotlin, ...).

Le principal avantage du typage statique est que de nombreuses vérifications peuvent être faites par le compilateur et permet de mettre en évidence des bugs "triviaux" très tot dans le processus de développement.

### Typage dynamique
Un lanage est à **typage dynamique** si le type est associé à des run-time values (et ce ne sont donc pas des variables?? voir poste de la source, **détail important**). Cela veut dire que le programmeur peut écrire plus rapidement car il ne doit pas spécifier les types (Perl, Ruby, Python, ...).

### Hybride
Un langage en cours de développement nommé [Rascal](http://www.rascal-mpl.org/) emprunte un approche hybride qui utilise un typage statique pour la signature d'une fonction mais qui permet un typage dynamique dans son corp.

## Langage compilé dynamiquement/statiquement
<span style="color:red"> A ne pas confondre avec le point précédent! </span>

**Compilation** dynamique et **typage** dynamique n'ont pas grand chose à voir l'un avec l'autre. Le **typage** fait partie de la syntaxe du langage alors que la **statégie de compilation** fait partie de l'implémentation du langage.

Un langage dynamiquement compilé veut dire que ce dernier est traduit (compilé) en langage machine pendant que le code est executé (**et non pas avant** l'execution). Ceci permet par exemple de faire du [JIT](https://en.wikipedia.org/wiki/Just-in-time_compilation) (just-in-time-optimization: optimisation du code pendant que l'application tourne).

### Précision
Le compilateur traduit le code source du C et C++ statiquement vers le langage machine.

Java est compilé vers du "bytecode" par la machine virtuelle de Java (Java compiler _peut être une confusion ici_). A l'execution, un programme java utilise un compilateur [JIT](https://en.wikipedia.org/wiki/Just-in-time_compilation) pour traduire le bytecode Java vers le langage machine natif du CPU de la machine qui execute le code.

**Compiler un programme vers le langage machine pendant que le programme tourne est appellé "compilation dynamique.**




## idiom

[stackoverflow](https://stackoverflow.com/questions/302459/what-is-a-programming-idiom)  

> An "idiom" in (non-programming) language is a saying or expression which is unique to a particular language. Generally something which doesn't follow the "rules" of the langauge, and just exist because native speakers "just know" what it means. 

Moving this to the programming arena, we get things like:

```c
if(c=GetValue())
    {...}
 ```
which actually means:

 ```C
c = GetValue();
if (c != 0)
    {....}
 ```

which every C/C++ programmer understand, but would totally baffle someone coming from a different programming language.

> In natural language an idiom is something whose meaning cannot be constructed from the meanings of its constituent terms. In other words it is an indivisible atom semantically even though syntactically it can be divided. So in programming, idioms are things you don't question but just rote-memorize and can be productive with.

## Sémantique
[wikipedia (general)](https://fr.wikipedia.org/wiki/S%C3%A9mantique)  
[wikipedia (IT)](https://fr.wikipedia.org/wiki/S%C3%A9mantique_des_langages_de_programmation)  


La sémantique est une branche de la linguistique qui étudie les signifiés, ce dont on parle, ce que l'on veut énoncer. Sa branche symétrique, la syntaxe, concerne pour sa part le signifiant, sa forme, sa langue, sa graphie, sa grammaire, etc ; c'est la forme de l'énoncé.

En particulier, la sémantique possède plusieurs objets d'étude :

* la signification des mots composés 
* les rapports de sens entre les mots (relations d'homonymie, de synonymie, d'antonymie, de polysémie, d'hyperonymie, d'hyponymie, etc.) 
* la distribution des actants au sein d'un énoncé 
* les conditions de vérité d'un énoncé 
* l'analyse critique du discours 
* la pragmatique, est considérée comme une branche de la sémantique.

**En informatique théorique, la sémantique formelle (des langages de programmation) est l’étude de la signification des programmes informatiques vus en tant qu’objets mathématiques.**

Il y a entre la sémantique et la syntaxe le même rapport qu'entre le fond et la forme.

## Process, Thread and Handle
[superuser](https://superuser.com/questions/1065826/handles-vs-threads-vs-processes)  


A Process is a isolated memory structure which supports an application in OS hardware and software. A Windows Process contains 1 or more Threads. [Wikipedia](https://en.wikipedia.org/wiki/Process_%28computing%29)

A Thread is a single set of sequential machine-code instructions that the processor executes. Any time the CPU runs an Instruction on behalf of an application, it does so via a thread. Threads within a process may access the processes memory (to the extent that the specific operation on the memory element is "thread-safe" and doesn't present unreconciled concurrency issues). A Process may speed its operation by using multiple threads, each performing an isolated task by running their stream of instructions through a different CPU Execution unit (CPU/core/virtual core) simultaneously.[Wikipedia](https://en.wikipedia.org/wiki/Thread_%28computing%29)

A Handle is a logical association with a shared resource like a file, Window, memory location, etc. When a thread opens a file, it establishes a "handle" to the file, and internally it acts like a "name" for that instance of the file. Handles are used to link to transitory or environmental resources outside the processes memory structure. A handle leak is a type of software issue that can in extreme cases, destabilize a system. It is caused by a program requesting a handle to a resource, and failing to deallocate it when the program is done with the resource. Based on your number however, I see nothing wrong there. [Wikipedia](https://en.wikipedia.org/wiki/Handle_%28computing%29)


## endiannes

> Ordre dans lequel les octets sont organisés dans une case mémoire **ou dans une communication**. Big endian et Little endian sont deux architectures différentes.

### Big endian
**byte de poids fort** à gauche.  
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:| 0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| A0 | B7 | 07 | 08 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| A0 | B7 | | 07 | 08 | ... |


<span style="color:#F92672">**Tous les protocoles TCP/IP communiquent en big-endian**</span>


### Little endian
**byte de poid faible** à gauche.   
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:|  0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| 08 | 07 | B7 | A0 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 07 | 08 | | A0 | B7 | ... |

<span style="color:#F92672">**X86 fonctionne en Little endian**</span>


### Search Engine Optmization (SEO)  
Search engines such as Google will drive traffic to your site if they think it’s relevant to what people are looking for




---------------------------------------------------------------------------







# Algo

## TAD 
Spécification mathématique d’un ensemble de valeurs et de l’ensemble des opérations qu’elles peuvent effectuer. Il est qualifié d’abstrait car il correspond à un cahier des charges qu’une structure de données doit ensuite implémenter.

## SCD
Implémentation concrète d’un TAD dans un langage.

## Heuristique 
Méthode qui fournit rapidement (temps polynomial) une solution réalisable mais pas forcément optimale pour un problème d’une complexité hors du possible pour l’approche statistique.

## Déterministe
Toute execution de cet algorithme sur les mêmes données donne lieu à la même suite d’opérations.

## Linéaire
il existe un premier et un dernier élément, tous les autres éléments ont un élément qui les précède et un autre qui les suit.

## Homogène 
Même type

## accès séquentiel
On accède aux données dans un ordre défini contrairement à un accès direct où on peut accéder à n’importe quel élément quand on veut.

## Dichotomique

On peut illustrer l'intérêt de la recherche dichotomique par l'exemple du jeu suivant.

A et B jouent au jeu suivant :

A choisit un nombre entre 0 et 100, et ne le communique pas à B, B doit trouver
ce nombre en posant des questions à A dont les réponses ne peuvent être que oui ou non. 

B doit essayer de poser le moins de questions possible. Une stratégie pour B est d'essayer tous les nombres, mais il peut aller plus rapidement comme le montre le scenario suivant :

A choisit 66 et attend les questions de B :
* B sait que le nombre est entre 0 et 100 

Au milieu se trouve 50, B demande donc :  
_Est-ce que le nombre est plus grand que 50?_
A répond _Oui_ .

* B sait maintenant que le nombre est entre 51 et 100, au milieu se trouve 75, il demande donc :

_Est-ce que le nombre est plus grand que 75?_
A répond  _Non_

* Et ainsi de suite : 

 _Plus grand que 63?_ ($ 63 =  \frac{51+75}{2}$) $ \Rightarrow $ _Oui_     
 _Plus grand que 69?_ ($ 69 =  \frac{63+75}{2}$) $ \Rightarrow $ _Non_     
 _Plus grand que 66?_ ($ 66 =  \frac{69+63}{2}$) $ \Rightarrow $ _Non_     
 _Plus grand que 65?_ ($ 65  \approx \frac{63+66}{2}$) $ \Rightarrow $ _Oui_     

* B sait maintenant que le nombre est entre 66 et 66, autrement dit qu'il s'agit de 66 : il a trouvé le nombre choisi par A en seulement 7 questions.



---------------------------------------------------------------------------



# Math

## Fonction monotone (monotonic function)
[wikipedia](https://fr.wikipedia.org/wiki/Fonction_monotone)

Une fonction monotone est une fonction numérique qui reste toujours soit croissante soit décroissante. **Son sens de variation est constant**.


<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/32/Monotonicity_example1.png/330px-Monotonicity_example1.png" align="left" height="150">
<br> <br> <span style="color:#888888">Fonction monotone croissante</span> <br> <br> <br> <br>

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/Monotonicity_example2.png/330px-Monotonicity_example2.png" align="left" height="150">
<br> <br> <span style="color:#888888">Fonction monotone décroissante</span> <br> <br> <br> <br>

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/Monotonicity_example3.png/330px-Monotonicity_example3.png" align="left" height="150">
<br> <br> <span style="color:#888888">Fonction qui n'est pas monotone</span> <br> <br> <br> <br> <br> <br>


# Quotes 

>The best way to solve a problem is often to redefine it.
>Michael Rabin