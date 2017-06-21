---
layout: post
title: "Cpp: Smart pointers et Move"
subtitle: "Conceptes et sémantique de déplacement"
date: 2017-06-21
author: Sol
category: Cpp
tags: Cpp
finished: false
---

# Introduction

Dans la fonction suivante...

```c++
void someFunction()
{
    Resource *ptr = new Resource; // Resource is a struct or class
 
    // do stuff with ptr here
 
    delete ptr;
}
```
... de nombreuses choses peuvent arriver qui vont empécher la réallocation de la mémoire.

Un `return`:

```c++
void someFunction()
{
    Resource *ptr = new Resource;
 
    int x;
    std::cout << "Enter an integer: ";
    std::cin >> x;
 
    if (x == 0)
        return; // the function returns early, and ptr won’t be deleted!
 
    // do stuff with ptr here
 
    delete ptr;
}
```
Un `throw`:

```c++
void someFunction()
{
    Resource *ptr = new Resource;
 
    int x;
    std::cout << "Enter an integer: ";
    std::cin >> x;
 
    if (x == 0)
        throw 0; // the function returns early, and ptr won’t be deleted!
 
    // do stuff with ptr here
 
    delete ptr;
}
```

De nombreux autres cas éxistent qui causent la fin de contexte prématuré de la fonction et l'empèche d'arriver au `delete`. 

**Conséquence**: la mémoire allouée pour la variable `ptr` n'est pas désallouée et il s'en suit une fuite de mémoire.

## God bless the classes !

Comme nous le savons, un détail très pratique des classes et le fait qu'elles soient équipées d'un destructeur qui s'exécute automatiquement quand un objet de la classe se retrouve hors portée (out of scope). Si nous allouons de la mémoire dans le constructeur et la déallouons dans le destructeur, nous avons la certitude que la mémoire sera restituée à la destruction de l'objet (peu importe si il sort de portée, est explicitement delete,...). Ce concepte est au coeur du paradigme de programmation [RAII](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization) (Resource Acquisition Is Initialization).

Considérons une classe dont le seul job est de "posséder" (own) un pointeur qu'on lui passe et de désallouer ce pointeur une fois que l'objet se retrouve hors context.

```c++
#include <iostream>
using namespace std;

template<typename T>
class Auto_ptr1 
{
    T* _ptr;
public:
    // on passe un pointeur à "posséder" à la cstrction
    Auto_ptr1(T* ptr=nullptr) :_ptr(ptr) {}

    // le dstr s'assure de la désallocation.

     ~Auto_ptr1() { delete _ptr; }

    // access aux données pointées
    T& operator *() const { return *_ptr; }
    T* operator ->() const { return _ptr; }
};

// classe de test
class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource detruites\n"; }
};

int main() {
    // allocation de mémoire...
    Auto_ptr1<Ressource> res{new Ressource}; 

    // ... mais pas de delte nécéssaire

    // De plus, à noter que Ressource entre chevrons ne requiert pas
    // de *     car c'est pris en charge par le template.
    
    return 0;
    // L'objet res se retrouve hors portée ici, est détruit
    // et nous récupérons les ressources allouées.
}

// output:

// Ressource acquise
// Ressource détruites

```

1. Création dynamique d'un objet de type **Ressource**
2. Passage de cet objet en paramètre lors de la création d'un objet (`res`) de type **Auto_ptr1**

<span style="color:red">À partir de ce moment la variable `res` **possède** (own) l'objet Ressource.</span>

Comme `res` est déclaré en tant que variable locale, sa portée est le bloc où elle est créé. Il se retrouvera hors de portée et **sera détruit** à la fin du bloc $$ \large \Rightarrow $$ pas à se soucier de sa désallocation.

> Aussi longtemps que **Auto_ptr1** est définit localement (pas global...), la destruction des ressources qu'elle possède sont assurées de destruction (même en cas de fin de contexte prématurée).

## Définition d'un smart pointer

Une classe de ce genre est appellée **smart\_pointer**. C'est une composition qui est faite pour gérer la mémoire allouée dynamiquement (simples pointeurs) et assurer que la mémoire est restituée quand le smart\_pointer se retrouve hors contexte.

## Exemple plus pratique

Reprenons l'exemple de la fonction `doSomething()` et voyons comment notre nouvelle classe gère les problèmes posés:

```c++
void fonction() {
    // variable ptr possède l'objet Ressource
    Auto_ptr1<Ressource> ptr(new Ressource);
    int x;
    cout << "Entrez un entier:";
    cin >> x;
    
    if (x == 0) { 
        return; // fin prématurée de la fonction
    }

    ptr->ditBonjour();
}

int main() {
    fonction();
    return 0;
}
```

ouput avec une input != 0:

```
Ressource acquise
Entrez un entier:4
Salut
Ressource detruites
```

output avec un input = 0:

```
Ressource acquise
Entrez un entier:0
Ressource detruites
```

Nous voyons donc ici que même dans le cas où la fonction est terminée de façon prématurée, les ressources sont quand même désallouées.

## Problèmes

```c++
int main() {
    Auto_ptr1<Ressource> res1{new Ressource};
    Auto_ptr1<Ressource> res2{res1};

    return 0;
}
```

Ce programme va éventuellement afficher ...
```
Ressource acquise
Ressource detruites
Ressource detruites
```

... avant de crasher

Le programme suivant aura le même résultat:

```c++
int main() {
    Auto_ptr1<Ressource> res1{new Ressource};
    Auto_ptr1<Ressource> res2;
    res2 = res1;

    return 0;
}
```

Comme nous ne fournissons pas de cstr de copies ou de srucharge de l'pd'assignation, C++ nous les génère par défaut. Et ces versions de  d'assignation surchargé base ne font qu'une **shallow copy**.

Quand nous initialisons `res`2 avec `res`1, les deux variables de type Auto_ptr1 pointent sur la même ressource et donc naturellement quand `res1` sort de portée, les ressources qu'il possède sont détruites et laisse `res2` avec un pointeur qui pointe dans le vide. Quand `res2` sort a son tour de portée il détruit des ressources déjà détruites et entraine un crash.

Le programme suivant nous produirait un résultat similaire:

```c++
void passByValue(Auto_ptr1<Ressource> res) { }
 
int main() {
	Auto_ptr1<Ressource> res1(new Ressource);
	passByValue(res1);
 
	return 0;
}
```

`res1` est copié par valeur en paramètre (`res`) de `passByValue()` entrainant une duplication de l'objet Ressource... crash.

* Une solution serait de `delete` le cstr de copies et la surcharge de l'p d'affectation et donc empécher les copies ce qui aurait pour efet  d'assignation surchargé de rêgler le problème des passages par valeur (ce qui d'un sens est une bonne chose, car il est évident qu'on ne passe pas ce genre de valeurs par valeur.)

    * $$ \large \Rightarrow $$ on se retrouve avec un nouveau problème... 
Sans le cstr de copie et la surcharge de l'p d'affectation, cmment  d'assignation surchargé retourner une valeur de type **Auto_ptr1** ?

```cpp
??? generateurDeRessource() {
    Ressource *r = new Ressource;
    return Auto_ptr(r);
}
```

* Le **retour par référence** n'est **pas** possible car la variable locales **Auto_ptr1** sera détruite à la fin du contexte de la fonction et la variable à l'autre bout de l'appel de la fonction se retrouvera avec une référence dans le vide.

* Le passage par adrèsse est ce qu'on cherche à éviter dans cet article donc on oublie aussi.

* L'option du passage par valeur comme vu plus haut, fait crasher le programme à cause des **shallow copy** et des pointeurs dupliqués.

* Personnaliser le cstr de copies et la surcharge de l'p d  d'assignation surchargé d'assignation en les faisant faire des **deep copy** fonctionnera mais **les copies sont couteuses en ressources** (et peuvent donc ne pas être désirable ou même possible en fonction de la complexité du problème) mais surtout nous ne voulons pas faire des copies _inutiles_ uniquement pour retourner un objet **Auto\_ptr1** d'une fonction. De plus assigner ou initialiser un _simple pointeur_ ne copie pas l'objet pointé donc pourquoi un _smart pointer_ agirait différament?

Bon bah... On fait quoi alors?

## Move semantics

À la place d'avoir notre cstr de copies ou notre oppérateur d'assignations qui copient le pointeur ("Sémantique de copie"), on peut à la place **transférer/déplacer** (move) la possession (ownership) du pointeur de la source à l'objet de destination.

Le concepte de  **sémentique de déplacement** (Move semantics) exprime qu'une classe va transférer la possèssion d'un objet à la place d'en faire une copie.

Faisons une mise à jour de notre classe **Auto_ptr1** pour illustrer ce concepte:

```c++
#include <iostream>
using namespace std;

template<typename T>
class Auto_ptr2
{
    T* _ptr;
public:
    Auto_ptr2(T* ptr=nullptr): _ptr(ptr) {}
    ~Auto_ptr2() { delete _ptr; }

    // cstr de copie qui implémente la sémantique de déplacement
    Auto_ptr2(Auto_ptr2 &a) // a n'est PAS const !
    {
        _ptr   = a._ptr;    // Transfer le ptr classique de source à obj local
        a._ptr = nullptr;   // On s'assure que la source ne possède plus le ptr
    }

    // OL de l'op d'assignation qui implémente la sémantique de déplacement
    Auto_ptr2& operator=(Auto_ptr2 &a) // a n'est PAS const !
    {
        if (&a == this) { // Self alloc check
            return *this;
        }

        delete _ptr; // On assure de la désallocation d'un 
                     // éventuel ptr possédé par la destination
        _ptr   = a._ptr;  // Transfer le ptr classique de source à obj local
        a._ptr = nullptr; // On s'assure que la source ne possède plus le ptr
        return *this;        
    }

    T& operator*() const { return *_ptr; }
    T* operator->() const { return _ptr; }
    bool isNull() const { return _ptr == nullptr; }
};


class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource detruites\n"; }
};


int main() {
    Auto_ptr2<Ressource> res1(new Ressource);
    Auto_ptr2<Ressource> res2; // initialement nullptr

    cout << "res1 est " << (res1.isNull() ? "null\n": "non null\n");
    cout << "res2 est " << (res2.isNull() ? "null\n": "non null\n");

    res2 = res1; // res2 reprend la possession, res1 est set null

    cout << "Transfère de possession fait\n";

    cout << "res1 est " << (res1.isNull() ? "null\n": "non null\n");
    cout << "res2 est " << (res2.isNull() ? "null\n": "non null\n");

    return 0;
}
```

output:

```
Ressource acquise
res1 est non null
res2 est null
Transfère de possession fait
res1 est null
res2 est non null
Ressource detruite
```

Notre surcharge de l'opérateur d'assignation a eu le résultat attendu et a bien transféré la possession de `_ptr` de `res1` à `res2`. Par conséquant on ne se retrouve pas avec des copies dupliqués du pointeur et tout se retrouve proprement nettoyé à la fin du programme.

## Un mot sur std::auto_ptr et pourquoi l'éviter

Jusqu'ici nous avons grossomodo implémenté ce qui ressemble à l'implémentation de std::auto_ptr de la version 98 de C++.

Même si c'est un bon cas d'école, cette implémentation est à éviter pour de nombreuses raisons. Plus d'info en bas de cette page: [learncpp.com](http://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/)

> std::auto_ptr est obsolet et ne devrait pas être utilisé. Il est dailleurs prévu que cette fonction de la STL soit retirée à la révision 17 de C++.

