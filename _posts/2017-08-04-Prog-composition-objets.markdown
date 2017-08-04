---
layout: post
title: "Prog: Relations entre objets"
subtitle: "OOD: Composition, aggregation et héritage"
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


# Introduction

La composition d'objets permet de créer des objets **complexes** en combinant des objets **simples**.

Une composition est une relation de type "$$ x \color{red}{\;a\;un\;} y $$" entre deux objets:

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


# Composition

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


Une composition est une relation **unidirectionelle** entre une `partie` et un `tout` où la partie est **contenue** dans le tout.

* `tout`   = objet (plus) **complexe** (eg. classe que nous définissons)
* `partie` = objet (plus) **simple** (eg. objet membre (attribut) qui sert a construire (fait partie de) la classe)

Pour être qualifiée de composition, un objet et une partie de cet objet doivent avoir les relation suivante:

1. La **partie** est une partie du **tout**.
2. La **partie** ne peut appartenir qu'à un **tout** à la fois.
3. L'existance de la **partie** est gérée par le **tout**.
4. la **partie** n'est pas "consciente" de l'existance du **tout**.

Exemple concrèt: La relation entre un `coeur` et un `corp`.

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

## Détails d'implémentation

Les composition qui font usage d'allocation dynamique devraient être implémentées en utilisant des attributs qui sont des pointeurs sur les objets qui composent la classe.

La composition devrait gérer elle-même la construction et la désallocation de la mémoire.

<span style="color:red"> Si ile design permet une relation de composition, il faut la faire.</span> 

Les classes qui sont des compositions sont "simples" (évidentes à comprendre), flexibles et robustes (elles nettoient derrière elles après utilisation)

## Exemple d'implémentation

Nous allons créer une classe Creature qui utilise une classe Point2D qui stock sa position.

### C++

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

### Même exemple en Python

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
