---
layout: post
title: "Prog: Relations entre objets"
subtitle: "Composition, aggregation et association"
date: 2017-04-04
author: Sol
category: Prog
tags: prog
finished: false
---

### Outils

* [uml in vscode](https://marketplace.visualstudio.com/items?itemName=JaimeOlivares.yuml)
* [Umbrello for linux](https://umbrello.kde.org/)
* [starUML (high-dpi support)](http://staruml.io/)


## Résumé

#### Composition
**"partie/tout"**, ($$ \textstyle x \color{red}{\;a\;un\;} y $$), **unidirectionnel** (**partie** ne sait pas qu'elle fait partie de **tout**)

* Les **parties** du **tout** sont des variables membres (attributs).
* Une **partie** ne peut pas appartenir à plusieurs classes.
* Le **tout** est en charge de la construction et destruction des **parties**.
* peut utiliser des pointeurs si le **tout** gere lui même l'allocation de la mémoire (construction et destruction).

<span style="color:red"> Si il est possible d'implémenter une classe comme une composition, il faut le faire ! </span> 


#### Aggregation
**"partie/tout"**, ($$ \textstyle x \color{red}{\;a\;un\;} y $$), **unidirectionnel**

* Les **parties** du **tout** sont des variables membres (attributs) qui sont des pointeurs ou des références sur des objets qui vivent en dehors du contexte du **tout**.
* Une **partie** peut appartenir à plusieurs classes.
* Le **tout** n'est pas responsable de la construction et de la destruction des **parties**.

#### Association 
**"pas reliés"**, ($$x \textstyle \color{red}{\;utilise\;un\;} y $$), **unidirectionnel** & **bidirectionnel**

* Un objet `B` est membre d'un objet `A` mais <span style="color:red">n'est pas une **partie**</span> de `A`.
* L'objet `B` n'a pas son existance gérée par la classe `A`.
* L'objet `B` peut être au courant de l'existance de l'objet `A` mais pas obligatoirement,

#### (Héritage)
**"lien de parenté**, ($$x \textstyle \color{red}{\;est\;un\;} y $$), 

* Un objet `B` qui hérite d'un objet `A` est un objet `A` plus spécialisé.



# Composition et association

La composition d'objets permet de créer des objets **complexes** en combinant des objets **simples**.

**Relation de type:**

$$ \bbox[20px,border:2px solid red]
{
    \large x \color{red}{\;a\;un\;} y
}
$$

**ou:**

$$ \bbox[20px,border:2px solid red]
{
    \large y \color{red}{\;fait\;partie\;\;de\;} x
}
$$

* Un ordinateur a un processeur
    * objet (plus) **complexe** $$ \Rightarrow $$ ordinateur
    * objet (plus) **simple** $$ \Rightarrow $$ processeur

* Une voiture a une transmission
    * objet (plus) **complexe** $$ \Rightarrow $$ voiture
    * objet (plus) **simple** $$ \Rightarrow $$ transmission

Il existe deux sous types de composition: 
* **Composition**
* **Aggregation**

> Le mot "Composition" est souvent utilisé pour faire référence aux deux. Dans cet article je vais faire référence à:
> * "composition d'objets" quand je fais référence à la composition en général
> * "composition" quand je parle du sous type

**Quand on parle d'une composition, on fait référence à une classe qui est une composition d'objets plus simples.**


## Composition

Une composition est une relation **unidirectionelle** entre une `partie` et un `tout` où la partie est **contenue** dans le tout.

* `tout`   = objet (plus) **complexe** (eg. classe que nous définissons)
* `partie` = objet (plus) **simple** (eg. objet membre (attribut) qui sert a construire (fait partie de) la classe)

Pour être qualifiée de composition, un objet et une partie de cet objet doivent avoir les relation suivante:

1. La **partie** est une partie du **tout**.
2. La **partie** ne peut appartenir qu'à un **tout** à la fois.
3. L'existance de la **partie** est gérée par le **tout**.
4. la **partie** n'est pas "consciente" de l'existance du **tout**.

**Exemple concret:** La relation entre un `coeur` et un `corp`.

1. Un **coeur** est une partie du **corp** d'une personne.
2. Ce **coeur** ne peut pas faire partie d'un autre **corp** en même temps.
3. Quand un **corp** est créé le **coeur** est créé en même temps. Quand un **corp** est "détruit" le **coeur** qui en fait partie l'est aussi.
4. Le **coeur** fonctionne sans être conscient qu'il fait partie d'une structure plus large (**corp**).

> La composition n'a rien à dire à propos de la transférabilité des parties. Un coeur peut être transplanté d'un corp à un autre. Après la transplantation, le nouveau corp et le coeur satisfont toujours les critères pour ếtre qualifiés de composition.

Exemple d'implémentation (c++)

```cpp
class Fraction {
private:
    int _num;
    int _den;
public:
    Fraction(int num=0, int den=1): _num(num), _den(den) {}
};  
```

Cette classe (**tout**) possède deux attributs (**partie**) membres:

* `_num` $$ \Rightarrow $$  un numérateur 
* `_den` $$ \Rightarrow $$  un dénominateur

1. Les deux sont des **parties** du **tout** (classe) `Fraction` (Elles sont contenues dans...).
2. Elle ne peuvent faire partie de plusieurs instances du **tout** (classe) `Fraction` à la fois.
3. Quand la classe `Fraction` est instanciée, `_num` et `_den` sont créées et quand l'instance est détruite, ce `_num` et ce `_den` le sont aussi.
4. `_num` et `_den` ne savent pas qu'elles font partie d'un objet `Fraction`, elles stockent juste un entier.

**Les parties d'une composition peuvent être unitaires ou multiples. Un coeur est une partie unique d'un corp mais un corp contient également 10 doigts (qu'on peut implémenter avec une liste par exemple).**

### Détails d'implémentation

Les composition qui font usage d'allocation dynamique devraient être implémentées en utilisant des attributs qui sont des pointeurs sur les objets qui composent la classe.

La composition devrait gérer elle-même la construction et la désallocation de la mémoire.

<span style="color:red"> Si ile design permet une relation de composition, il faut la faire.</span> 

Les classes qui sont des compositions sont "simples" (évidentes à comprendre), flexibles et robustes (elles nettoient derrière elles après utilisation)

### Exemple d'implémentation

Nous allons créer une classe Creature qui utilise une classe Point2D qui stock sa position.

#### C++

```cpp
#include<iostream>
#include<string>

class Point2D {
private:
    int _x;
    int _y;
public:
    Point2D(int x = 0, int y = 0) : _x(x), _y(y) { std::cout << "\t[instance de Point2D créé]\n"; }
    ~Point2D() { std::cout << "\t[instance de Point2D détruite]\n"; }

    void set_point(int x, int y) { _x = x; _y = y; }

    friend std::ostream& operator<<(std::ostream& out, const Point2D& p) {
        return out << "(" << p._x << "," << p._y << ")";
    }
};

class Creature {
private:
    std::string _name;
    Point2D _location;
public:
    Creature(std::string name, const Point2D& location): _name(name), _location(location) {
        std::cout << "\t[instance de Creature créé]\n";
    }
    ~Creature() { std::cout << "\t[instance de Creature détruite]\n"; }

    void move_to(int x, int y) { _location.set_point(x, y); }
    
    friend std::ostream& operator<<(std::ostream& out, const Creature& c) {
        return out << c._name << " est localisé en " << c._location;
    }
};

int main() {

    Creature c1{"George", Point2D(4, 6)};

    std::cout << "\n";
    std::cout << c1 << std::endl;

    std::cout << "\tc1.move_to(5,6);\n";
    c1.move_to(5,6);

    std::cout << c1 << std::endl;

    std::cout << "\n";
    return 0;
}
```

La classe `Point2D` est elle-même une composition des parties qui la compose. 
Les **valeurs** de `_x` et _y` sont des **parties** (attributs) du **tout** (classe) `Point2D`. Leur cycle de vie (construction, destruction) est lié à l'existance d'une instance Point2D.

De la même façon, la clase `Creature` est aussi une composition des parties qui la compose.
Une fois la classe `Creature instanciée, les **valeurs** de `\_name` et `\_location` appartiendront à un objet en particulier de type `Creature` et leur cycle de vie dépendra de cet objet.

**output**:

```
        [instance de Point2D créé]
        [instance de Creature créé]
        [instance de Point2D détruite]

George est localisé en (4,6)
        [c1.move_to(5,6);]
George est localisé en (5,6)

        [instance de Creature détruite]
        [instance de Point2D détruite]
```

#### Même exemple en Python

```python
class Point2D(object):
    def __init__(self, x, y):
        self._x = x
        self._y = y
        print("\t[instance de Point2D créé]")

    def __del__(self): print("\t[instance de Point2D détruite]")
    def __str__(self): return "({},{})".format(self._x, self._y)
    def set_point(self, x, y): self._x = x ;self._y = y

class Creature(object):
    def __init__(self, name, location):
        self._name = name
        self._location = location
        print("\t[instance de Creature créé]")

    def __del__(self): print("\t[instance de Creature détruite]")
    def __str__(self): return "{} est localisé en {}".format(self._name, self._location)
    def move_to(self, x, y): self._location.set_point(x, y)


c = Creature('George', Point2D(3,8))
print(c)
print("\t[c.move_to(12, 5)]")
c.move_to(12,5)
print(c)
```

**output:**

```
        [instance de Point2D créé]
        [instance de Creature créé]
George est localisé en (3,8)
        [c.move_to(12, 5)]
George est localisé en (12,5)
        [instance de Creature détruite]
        [instance de Point2D détruite]
```

### Variantes de composition

La majorité des compositions crées elles-même leurs parties (attributs) quand la composition est créé et les détruisent quand la composition est détruite mais il existe certaines variations possible:

Les compositions peuvent:

* Temporiser la création de certaines parties jusqu'à ce qu'on en ai besoin (eg. une classe `string` en C++ ne cré pas un tableau dynamique de `chars` avant que l'utilisateur ne lui assigne une chaîne de caractères)

* Préférer utiliser une partie qui leur est donné via _user input_ que d'en créer une elle meme.

* Déléguer la destruction de ses parties à un autre objet.

Le point important à garder en tête est que **la composition doit gérer ses parties** sans que l'utilisateur (peut être un objet) n'ai à faire quoi que se soit.

### Rule of thumb

* <span style="color:red"> **Chaque classe devrait être construite dans le but d'accomplir une des fonction suivante, idéalement pas les deux:** </span> 
    * <span style="color:red"> **Gestion et manipulation de données** </span> 
    * <span style="color:red"> **Coordination de sous-classe** </span> 

Dans l'exemple plus haut, les objets de type `Creature` n'ont pas à se soucier de comment sont implémenté les objets de types `Point2D` ou de comment les objets de type `string` alloue de la mémoire. Le job de la classe `Creature` est de **veiller à comment coordiner les données** venant des sous classes qui executent des tâches pour elle et le job de ces sous classes de s'inquiéter de **comment elles le font** !

## Aggregation

De la même façon qu'une composition, une aggregation est une relation **unidirectionelle** entre une **partie** et un **tout** où la **partie** est contenue dans le **tout**.

Contrairement à une composition, dans une aggregation:
* une **partie** peut faire partie de plusieurs **touts** en même temps
* Le **tout** n'est pas responsable du cycle de vie de ses **parties**

Pour être qualifiée d'aggregation, un objet et une partie de cet objet doivent avoir les relation suivante:

1. La **partie** est une partie du **tout**.
2. La **partie** peut appartenir à plusieurs **touts** à la fois
3. L’existance de la **partie** n'est pas gérée par un **tout**.
4. la **partie** n’est pas “consciente” de l’existance du **tout**.

**Exemples concrets:**   
La relation entre une adresse et une personne:

1. Chaque personne a une adresse,
2. Pourtant, cette adresse peut appartenir à plusieurs personnes à la fois.
3. Cette adresse n'est pas gérée par la personne qui y vit. L'adresse existait probablement avant que la personne y habite et existera toujours apès le départ de la personne.
4. Une personne sait à quelle adresse elle habite mais l'adresse ne sait pas qu'elle est habitée.

La relation entre un moteur et une voiture:

1. Un moteur fait partie d'une voiture.
2. Même si le moteur appartient à une voiture, il appartient également à d'autres entités, comme la personne à qui appartient la voiture par exemple.
3. La voiture n'est pas ressponsable de la création ou de la destruction du moteur. Une fois la voiture détruite, on peut imaginer que le moteur soit ajusté sur une autre voiture.
4. La voiture a besoin de savoir qu'elle a un moteur pour fonctionner. Le moteur quand à lui a juste besoin de faire ce que fait un moteur pour fonctionner, il n'a pas besoin de savoir qu'il fait partie d'une voiture... ou d'un avion...

**De la même façon qu'une composition, les parties d'une compositions peuvent être multiplicatives.**

### Détails d'implémentation

Une aggregation est similaire à une composition dans le sens où les deux sont une relation de type **partie/tout** et s'implémentent donc presque de la même façon. La différence est principalement semantique:

Dans une **composition**, on ajoute généralement les **parties** via des variables membres (attributs), ou en fonction du langage utilisé, des pointeurs avec une gestion de la mémoire dans le **tout**.

Dans une **aggregation**, on ajoute également les **parties** via des variables membres. Parcontre, **ces variables membre sont soit des références ou des pointeurs** utilisés pour pointer vers des objets créés en dehors du contexte du **tout**.

<span style="color:red"> **Une aggregation prend ses parties soit en argument lors de sa construction, soit débute sa vie sans parties et les parties sont ajoutées par la suite via des fonctions d'acces.** </span> 

Comme ces **parties** existent en dehors du contexte du **tout**, quand l'instance issue de ce **tout** est détruit, les pointeurs ou les références à ses **parties** le sont aussi. <span style="color:red"> Les **parties** en elles-mêmes existent toujours! </span>

### Exemples d'implémentation

Relation entre une école et un professeur.

Dans cette exemple nous allons simplifier la situation en assumant que l'école n'a qu'un professeur et que ce dernier n'est pas au courant de l'école dans laquelle il est.

#### C++

```cpp
#include<iostream>

class Professeur {
private:
    std::string _name;
public:
    Professeur(std::string name): _name(name) { 
        std::cout << "\t[Professeur construit]\n"; 
    }
    ~Professeur(){ std::cout << "\t[Professeur detruit]\n"; } 
    std::string get_name() { return _name; }
};

class Ecole {
private:
    Professeur *_professeur;
public:
  Ecole(Professeur *professeur = nullptr) : _professeur(professeur) { 
      std::cout << "\t[Ecole construite]\n"; 
    }
  ~Ecole() { std::cout << "\t[Ecole detruite]\n"; }
};

int main() {
    // Creation d'un professeur en dehors du contexte de l'école
    std::cout << "[professeur *professeur = new professeur(\"Bob\");]\n";
    Professeur *professeur = new Professeur("Bob");

    // Creation d'une école et passage d'un professeur 
    // en parametre de son constructeur.
    {
        std::cout << "[Ecole ecole(professeur);]\n";
        Ecole ecole(professeur);
        
        std::cout << "[L'école sort de contexte ici et est détruite]\n";
    }

    // Le professeur existe toujours ici car l'école n'est
    // pas en charge de sa destruction.

    std::cout << professeur->get_name() << " existe toujours!\n";

    std::cout << "[delete professeur;]\n";
    delete professeur;

    return 0;
}

```

**output:**

```
[professeur *professeur = new professeur("Bob");]
        [Professeur construit]
[Ecole ecole(professeur);]
        [Ecole construite]
[L'école sort de contexte ici et est détruite]
        [Ecole detruite]
Bob existe toujours!
[delete professeur;]
        [Professeur detruit]
```

Le professeur est créé indépendament de l'école et est passé au constructeur de l'école. Quand l'école est détruite, le pointeur sur `_teacher` est détruit mais pas l'instance. Elle ne sera détruite qu'au moment ou nous le demanderons explicitement via `delete`.

Même si dans le cadre de cet exemple cela semble improbable que le professeur ne sache pas dans quelle école il travail, dans un contexte programmatique différent c'est une situation commune.

#### Python

```python
class Professeur(object):
    def __init__(self, name):
        self._name = name
        print("\t[Professeur construit]\n")

    def __del__(self): print("\t[Professeur detruit]\n")

    def get_name(self): return self._name

class Ecole(object):
    def __init__(self, professeur):
        self._professeur = professeur
        print("\t[Ecole construite]\n")

    def __del__(self): print("\t[Ecole detruite]\n")


# Creation d'un professeur en dehors du contexte de l'école.
print('[professeur = Professeur("Bob")]')
professeur = Professeur("Bob")


# Creation d'une école et passage d'un professeur
# en parametre de son constructeur.
print("[ecole = Ecole(teacher)]")
ecole = Ecole(professeur)

# Destruction de l'instance dept.
print("[del ecole]")
del ecole

# Le professeur existe toujour ici car l'école n'est
# pas en charge de sa destruction.
print("{} existe toujours".format(professeur.get_name()))

print("[del professeur]")
del professeur

```

**output:**

```
[professeur = Professeur("Bob")]
        [Professeur construit]

[ecole = Ecole(teacher)]
        [Ecole construite]

[del ecole]
        [Ecole detruite]

Bob existe toujours
[del professeur]
        [Professeur detruit]
```

## Choisir la bonne relation

Quand on chercher a déterminer le type de relation à utiliser pour une implémentation, il faut utiliser la plus simple qui correspond à nos besoins et non pas celle qui correspond à la réalité des objets physiques.

Si nous écrivons un programme qui simule un garrage, on pourrait utiliser une aggregation pour avoir un moteur qui peut être retiré d'une voiture et ajusté dans un autre. Parcontre, si nous codons un jeu de courses, il peut être préférable d'implémenter une voiture et un moteur comme une composition car le moteur n'existera jamais séparé de la voiture.

### Rule of thumb

<span style="color:red"> Toujours implémenter la relation la plus simple qui correspond à nos besoins et non pas ce qui semble jute IRL. </span> 

## Combo

Nous pouvons mélenger dans un même **tout** (classe) composition et aggregation. C'est à dire avoir certaines **parties** (attributs) de ce **tout** dont la construction/destruction est géré par le **tout** et d'autres **parties** qui vivent en dehors du contexte du **tout**.

**Exemple**:
La classe `Ecole` pourrait avoir comme attributs un nom et un professeur.
* `_nom` pourrait être implémenté sous forme de **composition** et cet attribut serait construit et détruit en même temps que les instances d'`Ecole`.
* `_professeur` pourrait être implémenté sous forme d'**aggregation** et être construit et détruit indépendament des instances d'`Ecole`.


# Association

Contrairement à la composition où une **partie** fait partie d'un **tout**, dans une association, un objet de type `B` qu'on associe à un objet de type `A` ne fait pas forcément partie de ce dernier. Tout comme dans une aggregation, l'objet associé (`B`) peut appartenir simultanément à plusieurs objets et son existance n'est pas géré par 'A'. Parcontre, dans une association, la relation peut être unidirectionelle **ou** bidirectionelle (Les deux objets sont au courant de leur existance reciproquement).

Pour être qualifié d'association la relation entre un objet de type `B` qu'on associe à un objet de type `A` doit être la suivante:

1. L'objet `B` membre de `A` n'est pas une partie de `A` (il a sa vie à lui et peut faire plein de choses) mais il a une relation avec l'objet `B`.
2. L'objet `B` et l'objet `A` peuvent appartenir à plusieurs classes à la fois.
3. L'objet `B` n'a pas son existance gérée par une autre classe.
4. L'objet `B` peut être au courant de l'existance de l'objet `A` mais pas obligatoirement

**Exemple concret:**: La relation entre un **avocat** et son **client**:

1. L'**avocat** a une relation avec son **client** mais ne fait pas partie du **client**.
2. Un **avocat** peut voir plusieurs clients différents et le **client** peut voir plusieurs avocats différents.
3. Le clycle de vie du **client** n'est pas géré par le **client** et vice versa.
4. Un **client** et un **avocat** peuvent ne pas se connaitre (Avant de se recontrer par exemple).

**Relation de type:**

$$ \bbox[20px,border:2px solid red]
{
    \large x \color{red}{\;utilise\;un\;} y
}
$$

Dans notre exemple, l'**avocat** "utilise" le **client** (pour gagner de l'argent) et le **client** "utilise" l'**avocat** (pour ses soucis juridiques).

## Implémentation sous forme d'association

Les associations sont une forme de relation très vaste et il existe de nombreuses façons de les implémenter. Nous allons implémenter la relation avocat/client sous forme d'une relation **bidirectionelle** car cela semble évident que l'avocat sache qui est son client et vice-versa.

> Ce type de relation bidirectionelle pose problème en C++ à cause de la dépendance circulaire. En Python nous n'avons pas ce problème grace à son typage (Duck-typing), ce qui me permet de présenter un exemple propre et parlant.

```python
class Client(object):
    def __init__(self, nom):
        self._nom = nom
        self._avocats = []

    def __str__(self):
        if (len(self._avocats) == 0):
            return "{} n'a pas d'avocats pour le moment.".format(self._nom)
        else:
            return "{} voit les avocats suivants: {}".format(
                self._nom, ', '.join(i.get_name() for i in self._avocats))

    def add_avocat(self, Avocat):

        # Le client ajoute un avocat si ce dernier n'est 
        # pas déja dans sa liste d'avocats...
        if Avocat.get_name() not in [i.get_name() for i in self._avocats]:
            self._avocats.append(Avocat)

            # ... ce qui veut dire que l'avocat ajoute
            # également ce Client à sa liste de clients !
            Avocat.add_client(self)

    def get_name(self):
        return self._nom


class Avocat(object):
    def __init__(self, nom):
        self._nom = nom
        self._clients = []

    def __str__(self):
        if (len(self._clients) == 0):
            return "{} n'a pas de clients pour le moment".format(self._nom)
        else:
            return "{} a les clients suivants: {}".format(
                self._nom, ', '.join(i.get_name() for i in self._clients))

    def add_client(self, Client):

        # L'avocat ajoute un client si ce dernier n'est 
        # pas déja dans sa liste de clients...
        if Client.get_name() not in [i.get_name() for i in self._clients]:
            self._clients.append(Client)

            # ... ce qui veut dire que le client ajoute 
            # également cet avocat à sa liste d'avocats.
            Client.add_avocat(self)

    def get_name(self):
        return self._nom

bob = Client("Bob")
jon = Client("Jon")
pol = Client("Pol")

claude = Avocat("Claude")
robert = Avocat("Robert")

claude.add_client(bob)

robert.add_client(jon)
robert.add_client(pol)

bob.add_avocat(robert)

print(claude)
print(robert)
print(bob)
print(jon)
print(pol)
```

**output:**

```
Bob n'a pas d'avocats pour le moment.
Robert n'a pas de clients pour le moment
Claude a les clients suivants: Bob
Robert a les clients suivants: Jon, Pol, Bob
Bob voit les avocats suivants: Claude, Robert
Jon voit les avocats suivants: Robert
Pol voit les avocats suivants: Robert
```

## Association reflexive (objets différents mais du même type)

C'est une relation entre **objets différents mais du même type**. Un exemple qui illustre ce cas est celui d'un cours et les prérequis pour ces cours (qui sont eux même des cours). Voici un cas simplifié de cet exemple:

```python
class Cours(object):
    def __init__(self, nom, *prerequis):
        self._prerequis = [i for i in prerequis]
        self._nom = nom

    def __str__(self):
        temp = "Nom du cours: {}\nPrerequis: {}\n"
        if len(self._prerequis) == 0:
            return temp.format(self._nom, "aucun")

        return temp.format(self._nom, ', '.join(i._nom for i in self._prerequis))


arithmetique = Cours("arithmetique")

algebre1 = Cours("algebre1", arithmetique)
algebre2 = Cours("algebre2", algebre1)
trigo    = Cours("trigo", algebre1)

analyse1 = Cours("analyse1", algebre2, trigo)

print(analyse1)
print(trigo)
print(algebre1)
print(arithmetique)
```

**output:**

```
Nom du cours: analyse1
Prerequis: algebre2, trigo

Nom du cours: trigo
Prerequis: algebre1

Nom du cours: algebre1
Prerequis: arithmetique

Nom du cours: arithmetique
Prerequis: aucun
```

Ici nous avons donc créé une chaîne d'associations entre objets du même type. Un cours a un prérequis, qui a lui même un prérequis, qui a lui même un prérequis...