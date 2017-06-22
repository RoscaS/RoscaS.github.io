---
layout: post
title: "Cpp: Sémantique de déplacement & smart_ptr"
subtitle: "Introduction aux smart_ptr et à la sémantique de déplacement"
date: 2017-06-21
author: Sol
category: Cpp
tags: Cpp
finished: false
---

## Introduction

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

Comme nous ne fournissons pas de cstr de copies ou de srucharge de l'operateur d'affectation, C++ nous les génère par défaut. Ces versions de base ne font qu'une **shallow copy**.

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

* Une solution serait de `delete` le cstr de copies et la surcharge de l'operateur d'affectation. Cela aurait pour effet d'empécher les copies et donc de rêgler le problème des passages par valeur (ce qui d'un sens est une bonne chose, car il est évident qu'on ne passe pas ce genre de valeurs par valeur.)

    * $$ \large \Rightarrow $$ on se retrouve avec un nouveau problème... 
Sans le cstr de copie ou de surcharge de l'opérateur d'affectation, comment  retourner une valeur de type **Auto_ptr1** ?

```cpp
??? generateurDeRessource() {
    Ressource *r = new Ressource;
    return Auto_ptr(r);
}
```

* Le **retour par référence** n'est **pas** possible car la variable locales **Auto_ptr1** sera détruite à la fin du contexte de la fonction et la variable à l'autre bout de l'appel de la fonction se retrouvera avec une référence dans le vide.

* Le passage par adrèsse est ce qu'on cherche à éviter dans cet article donc on oublie aussi.

* L'option du passage par valeur comme vu plus haut, fait crasher le programme à cause des **shallow copy** et des pointeurs dupliqués.

* Personnaliser le cstr de copies et la surcharge de l'opérateur d'affectation en s'assurant qu'ils facent des **deep copy** fonctionnerait mais **les copies sont couteuses en ressources** (et peuvent donc ne pas être désirable ou même possible en fonction de la complexité du problème) mais surtout nous ne voulons pas faire des copies _inutiles_ uniquement pour retourner un objet **Auto\_ptr1** d'une fonction. De plus assigner ou initialiser un _simple pointeur_ ne copie pas l'objet pointé donc pourquoi un _smart pointer_ agirait différament?

Bon bah... On fait quoi alors?

## Move semantics

À la place d'avoir notre cstr de copies ou notre oppérateur d'affectation qui copient le pointeur ("Sémantique de copie"), on peut à la place **transférer/déplacer** (move) la possession (ownership) du pointeur de la source à l'objet de destination.

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

    // OL de l'op d'affectation qui implémente la sémantique de déplacement
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

Notre surcharge de l'opérateur d'affectation a eu le résultat attendu et a bien transféré la possession de `_ptr` de `res1` à `res2`. Par conséquant on ne se retrouve pas avec des copies dupliqués du pointeur et tout se retrouve proprement nettoyé à la fin du programme.

## Un mot sur std::auto_ptr et pourquoi l'éviter

Jusqu'ici nous avons grossomodo implémenté ce qui ressemble à l'implémentation de std::auto_ptr de la version 98 de C++.

Même si c'est un bon cas d'école, cette implémentation est à éviter pour de nombreuses raisons. Plus d'info en bas de cette page: [learncpp.com](http://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/)

> std::auto_ptr est obsolet et ne devrait pas être utilisé. Il est dailleurs prévu que cette fonction de la STL soit retirée à la révision 17 de C++.

## Références sur R-value et L-value

Avant d'aller plus loin il est interessant de vite fait voir ce qui se passe sur [cette](\cpp\cpp-R-value-L-value.html) page un peu barbante mais utile pour la suite.


## Sémantique de copie

Et puis sur [celle-ci](\cpp\cpp-semantiqueCopie.html) aussi tant qu'à faire...


## Constructeur de copies et opérateur d'affectation

* Le **constructeur de copie** est utilisé pour initialiser une classe en faisant une copie d'un objet de la même classe.

* La **surcharge de l'opérateur d'affectation** est utilisée pour copier les membres d'un objet dans un autre objet déjà existant.

* Par défaut, le compilateur génère un constructeur de copie et opérateur d'affectation si **l'un des deux** n'est explicitement fournit. 

* Ces fonctions fournies par le compilateur font des **shallow copies** ce qui peut causer des problèmes pour les classes qui allouent dynamiquement de la mémoire.

* <span style="color:red">Les classes qui allouent dynamiquement de la mémoire **doivent** fournir des fonctions qui font des **deep copy** à la place ! </span>


Reprenons l'exemple de la classe `Auto_ptr` et ajoutons y un constructeur de copie et une surcharge de l'opérateur d'affectation pour respecter le dernier point.

```cpp
#include<iostream>
using namespace std;

template<typename T>
class Auto_ptr3
{
    T* _ptr;
public:
    Auto_ptr3(T* ptr=nullptr): _ptr(ptr) {}
    ~Auto_ptr3() { delete _ptr; }

    // cstr de copie, deep copie a._ptr dans _ptr
    Auto_ptr3(const Auto_ptr3 &a) {
        _ptr  = new T;
        *_ptr = *a._ptr;
    }

    // surcharge opérateur d'affectation, deep copie a._ptr dans _ptr
    Auto_ptr3& operator=(const Auto_ptr3 &a) {
        // self assign-check
        if (&a == this) {
            return *this;
        }
        // désalloue d'éventuelles ressources
        delete _ptr;
        // copie les ressources
        _ptr = new T;
        *_ptr = *a._ptr;

        return *this;
    }

    T* operator->() const { return _ptr; }
    T& operator*()  const { return *_ptr; }
    bool isNull()   const { return _ptr == nullptr; }   
};

class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource detruite\n"; }
};

Auto_ptr3<Ressource> generationRessource() {
    Auto_ptr3<Ressource> res(new Ressource);
    return res; // cette valeur de retour invoque le cstr de copie
}

int main() {
    Auto_ptr3<Ressource> mainRes;
    mainRes = generationRessource(); // cette affectation invoque 
                                    // la surcharge de l'op d'affectation
    return 0;
}
```

Dans ce programme la fonction `generationRessource()` crée une ressource encapsulée dans un smart pointer qui est ensuite retourné à la fonction `main()` et affectée à un objet Auto_ptr3 préexistant.

L'output est:

```
Ressource acquise
Ressource acquise
Ressource detruite
Ressource acquise
Ressource detruite
Ressource detruite
```
... beaucoup de ressources créées et détruites pour un si simple programme. Si nous décortiquons ce dernier, nous pouvons identifier 6 étapes clés (une par message printé).

1. Dans la fonction `generationRessource()`, la variable locale `res` est créée et initialisée avec des ressources allouées dynamiquement $$\Rightarrow$$ premier message $$Ressource\;acquise$$

2. `res` est retourné dans le `main()` par copie de valeur. On retourne de cette façon car **res** est une variable locale à la fonction et une référence à cette dernière pointerait dans le vide une fois la fonction finie. `Res` est donc construit par copie dans un **objet temporaire**. Comme notre cstr de copie fait une **deep copie**, une nouvelle Ressource est allouée d'où le second message $$Ressource\;acquise$$.

3. `res` sort de portée et donc la Ressource initiale est détruite ce qui cause le premièr message $$Ressource\;detruite$$.

4. L'objet temporaire est assigné à la variable `mainRes` par **assignement par copie**. Comme notre cstr de copies gère la **deep copy**, une nouvelle ressource est allouée et causant le 3e message $$Ressource\;acquise$$.

5. L'expression d'affectation finie, et l'**objet temporaire** se retrouve hors de portée et détruit d'où le second message $$Ressource\;detruite$$.

6. À la fin du `main()`, `mainRes` sors lui aussi de portée et génère notre dernier message $$Ressource\;detruite$$.

<span style="color:red"> bien comprendre les points 2 et 5 ! </span>

Résultat des courses, pour un appel du constructeur de copie pour copier `res` dans un temporaire et une affectation du temporaire dans le `main()` dans la variable `resMain` on se retrouve avec 3 objets différents au total.

Pour le moins **innéfficient** mais au moins ça ne crash pas!

## Constructeur de déplacement et affectation de déplacement

C++ 11 définit deux nouvelles fonctions au service de la sémantique de déplacement:

* Un constructeur de déplacement
* Une surcharge de l'opérateur d'affectation pour le déplacement

Leur définition fonctionne de façon analogue à celle du cstr de copie et la surcharge de l'opérateur d'affectation à la différence que les fonctions dédiées au déplacement prennent des références à des R-value **non-constantes** à la place des R-value constantes de leur homologues de copie.

Voici notre fonction Auto_ptr améliorée de la capacité de déplacement (la paire de fonctions dédiées à la copie sont toujours là pour la comparaison).

```cpp
#include<iostream>
using namespace std;

template<typename T>
class Auto_ptr4
{
    T* _ptr;
public:
    Auto_ptr4(T* ptr=nullptr): _ptr(ptr) {}
    ~Auto_ptr4() { delete _ptr; }

    // cstr de copie, deep copie a._ptr dans _ptr
    Auto_ptr4(const Auto_ptr4 &a) {
        _ptr  = new T;
        *_ptr = *a._ptr;
    }

    // cstr de déplacement, transfère la possèssion de a._ptr a _ptr
    Auto_ptr4(Auto_ptr4 &&a): _ptr(a._ptr) {
        a._ptr = nullptr;
    }

    // surcharge opérateur d'affectation, deep copie a._ptr dans _ptr
    Auto_ptr4& operator=(const Auto_ptr4 &a) {
        // self assign-check
        if (&a == this) {
            return *this;
        }
        // désalloue d'éventuelles ressources
        delete _ptr;
        // copie les ressources
        _ptr  = new T;
        *_ptr = *a._ptr;

        return *this;
    }

    // Affectation de déplcement, transfère la possèssion de a._ptr a _ptr
    Auto_ptr4& operator=(Auto_ptr4 &&a) {
        // self assign-check
        if (&a == this) {
            return *this;
        }
        // désalloue d'éventuelles ressources
        delete _ptr;
        // transfère la possèssion de a._ptr a _ptr
        _ptr   = a._ptr;
        a._ptr = nullptr;

        return *this; 
    }


    T* operator->() const { return _ptr; }
    T& operator*()  const { return *_ptr; }
    bool isNull()   const { return _ptr == nullptr; }   
};

class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource detruite\n"; }
};

Auto_ptr4<Ressource> generationRessource() {
    Auto_ptr4<Ressource> res(new Ressource);
    return res; // cette valeur de retour invoque le cstr de copie
}

int main() {
    Auto_ptr4<Ressource> mainRes;
    mainRes = generationRessource(); // cette affectation invoque 
                                    // la surcharge de l'op d'affectation
    return 0;
}
```
Ces nouvelles fonctions sont très simples. À la place de **deep copy** l'objet source (a) dans le nouvel objet, ils se contente de bouger (voler) les ressoues de l'objet source. Ceci implique une **shallow copy** du pointeur de la source dans l'objet et un set du pointeur de la source à `nullptr`.

Le nouvel output ressemble à:

```
Ressource acquise
Ressource detruite
```

Beaucoup mieux !

Le flow du programme reste le même que le précédent sauf qu'à la place d'appeler le constructeur de copie et l'opérateur d'affectation, ce programme appel les homologues dédiés au déplacement.

1. Dans la fonction `generationRessource()`, la variable locale `res` est créée et initialisée avec des ressources allouées dynamiquement $$\Rightarrow$$ premier message $$Ressource\;acquise$$

2. `res` est retourné dans le `main()` par valeur: `res` via le cstr de déplacement est transféré dans un objet temporaire créé dynamiquement.

3. `res` sort de portée. Comme `res` ne possède plus de pointeur (car déplacé dans l'objet temporaire), nous n'avons pas de print.

4. L'objet temporaire est assigné à la variable `mainRes` ce qui transfère l'objet créé dynamiquement et contenu dans la variable temporaire à `mainRes`.

5. L'expression d'affectation finie, l'**objet temporaire** se retrouve hors de portée et est détruit mais comme cet objet temporaire à transféré son contenu, il ne possède plus de pointeur (il est maintenant dans `mainRes`) et lors de cette destruction, nous n'avons pas de print no plus.

6. À la fin du `main()`, `mainRes` sors de portée et génère le message $$Ressource\;detruite$$.

Résultat des courses, à la place de copier notre ressource deux fois, nous la transfèrons deux fois. <span style="color:red">Ce qui est plus efficace au niveau ressources.</span>

La paire dédiée au déplacement n'est pas fournie par défaut.

## Règle

<span style="color:red">Pour utiliser le constructeur de déplacement et l'affectation de déplacement nous devons les fournir nous même.</span>

## Quand est la paire dédiée au déplacement appelée ?
Lorsque ces fonctions sont définies et que l'argument pour la construction ou l'affectation est une R-value. Cette R-value est typiquement un literal ou une valeur temporaire.

## Copie VS déplacement 

### Copie
Si nous construisons ou affectons un objet et que l'argument est une L-value, il est préférable de copier cette L-value. On ne peut assumer qu'il est safe de la modifier car on pourait en avoir besoin plus tard dans le programme.

Dans le cas d'une exression comme `a = b`, il est impensable que `b` change de valeur.

### déplacement
Si nous construisons ou affectons un objet et que l'argument est une R-value, nous savons que cette R-value n'est d'une façon ou d'une autre qu'un objet temporaire. À la place de le copier (ce qui peut couter cher en ressources), on peut simplement transférer les ressources qu'il possède (bien moins couteux en ressources) à l'objet que nous construisons ou affectons. 

C'est safe car de toutes façons l'objet temporaire ser détruit à la fin de l'expression et que donc nous savons que nous n'allons pas la réutiliser.

> C++11, grâce au références R-value nous permet de donner des comportements différents si l'argument est une R-value ou une L-value. Cela nous permet d'avoir plus de liberté et d'être plus intéligent dans le choix des comportement de nos objets.

## Empécher la copie
Dans notre version de la class Auto_ptr précédent, nous avons laissé la paire de copie pour pouvoir visuellement comparer les deux paires.

Dans les classes qui sont capable de faire des déplacements, il parfois désirable de **delete**  la paire de copie pour s'assurer qu'aucune copie ne sera faite.

Dans le cas de notre exemple, nous ne voulons pas que notre objet générique `T` soit copié. D'une part car c'est couteux en ressources et d'autre part car en fonction du type (classe) `T` utilisé, il se peut que cette dernière ne supporte pas la copie.

Voici une version de la classe Auto_ptr qui gère la sémantique de déplacement mais pas celle de copie:

```cpp
template<typename T>
class Auto_ptr5
{
    T* _ptr;
public:
    Auto_ptr5(T* ptr=nullptr): _ptr(ptr) {}
    ~Auto_ptr5() { delete _ptr; }

    // cstr de copie, copies non supportées !
    Auto_ptr5(const Auto_ptr5 &a) = delete;

    // cstr de déplacement, transfère la possèssion de a._ptr a _ptr
    Auto_ptr5(Auto_ptr5 &&a): _ptr(a._ptr) {
        a._ptr = nullptr;
    }

    // surcharge opérateur d'affectation, copies non supportées !
    Auto_ptr5& operator=(const Auto_ptr5 &a) = delete;

    // Affectation de déplcement, transfère la possèssion de a._ptr a _ptr
    Auto_ptr5& operator=(Auto_ptr5 &&a) {
        // self assign-check
        if (&a == this) {
            return *this;
        }
        // désalloue d'éventuelles ressources
        delete _ptr;
        // transfère la possèssion de a._ptr a _ptr
        _ptr   = a._ptr;
        a._ptr = nullptr;

        return *this; 
    }


    T* operator->() const { return _ptr; }
    T& operator*()  const { return *_ptr; }
    bool isNull()   const { return _ptr == nullptr; }   
};
```

Si nous tentons de passer une L-value de type Auto_ptr5 à une fonction, le compilateur se pleindrait que le constructeur de copie requis pour l'initialisation de copies a été delete. C'est une bonne chose car nous ne devrions pas passer Auto\_ptr5 par référence L-value constante de toutes façon !

Auto_ptr5 est (finalement) une bonne classe smart pointer. En réalité celle que contient la STL (std::unique\_ptr) lui ressemble beaucoup.

## Dernier exemple

