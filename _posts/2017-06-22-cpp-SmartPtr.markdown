---
layout: post
title: "Cpp: unique_ptr & shared_ptr"
subtitle: 
date: 2017-06-22
author: Sol
category: Cpp
tags: cpp 
finished: false
---

## Lecture utile avant de se lancer dans ce poste

* [L-value et R-value](\cpp\cpp-R-value-L-value.html)
* [Sémantique de copie](\cpp\cpp-semantiqueCopie.html) 
* [Sémantique de déplacement](\cpp\cpp-move&smartP.html)  
Ce dernier point est un prérequis plus qu'un conseil.

## sources

* [learncpp.com](http://www.learncpp.com/cpp-tutorial/15-5-stdunique_ptr/) (ch 15.1 à 15.7)
* [stackoverflow](https://stackoverflow.com/)
* [wikipedia](https://fr.wikipedia.org/wiki/S%C3%A9mantique)

# std::unique_ptr

Au début de [cet](\cpp\cpp-move&smartP.html) article on a vu les dangers de l'utilisation des pointeurs. Après avoir couvert les bases de la sémantique de déplacement dans le même poste nous pouvons maintenant tenter de cérner les smart pointer.

## rappel

> Un smart ponter est une classe qui gère (possède, own) un objet alloué dynamiquement et s'assure que l'objet est est proprement néttoyé en fin de vie (généralement quand il sort de portée).
>
> À cause de cette propriété, un smart pointeur **ne doit jamais** être lui même dynamiquement alloué. Si c'était le cas, tout le mécanisme de gestion serait saboté et on se retrouverait à coup sur avec des fuites de mémoire. Si l'allocation est toujours faite statiquement (en tant que variable locale où "composition member of a class"), il est garantis que le smart pointer se retrouvera lui même hors de portée lorsque l'objet qu'il contient se retrouvera hors de portée assurant le néttoyage de la ressource et du smart pointer.

La librairie de C++ 11 possède 4 types de smart pointer:
* `std::auto_ptr` à fuir comme la peste (sera retiré à la révision 17)
* `std::unique_ptr` qui remplace efficacement le précédent et le plus utilisé des 4
* `std::shared_ptr`
* `std::weak_ptr`

## std::unique_ptr

Utilisé pour gérer dynamiquement un objet alloué dynamiquement mais dont unique_ptr est le seul possésseur.

`std::unique_ptr` vit dans les header `memory`

Considérons cet exemple:

```cpp
#include<iostream>
#include<memory>    // pour std::unique_ptr
using namespace std;

class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource détruite\n"; }
};


int main() {
	unique_ptr<Ressource> res1(new Ressource); // Ressource créé ici
	unique_ptr<Ressource> res2; // commence comme nullptr
 

    // res2 = res1; // ne compilera pas: affectation par copie disabled
	cout << "res1 est" << (static_cast<bool>(res1) ? "non null\n" : "null\n");
	cout << "res2 est" << (static_cast<bool>(res2) ? "non null\n" : "null\n");
 
	res2 = move(res1); // res2 reprend l'ownership, res1 est set à null
 
	cout << "Ownership transferred\n";
 
	cout << "res1 est" << (static_cast<bool>(res1) ? "non null\n" : "null\n");
	cout << "res2 est" << (static_cast<bool>(res2) ? "non null\n" : "null\n");

    return 0;
}
```

Comme `unique_ptr` a été conçu avec la sémantique de déplacement en tête, si nous voulons transférer l'ownership d'un unique_ptr à un autre nous **devons** utiliser la sémantique de déplacement. Dans l'exemple plus haut nous faisons ça via la fonction `std::move()` qui converti res1 en R-value et force l'utilisation de la sémantique de déplacement.

## Accès aux membres

Comme les pointeurs traditionnels:

* `*` retourne une référence de la ressource possédée
* `->` retourne un pointeur sur la ressource possédée

> Il est important de garder en tête que pour de multiples raisons unique_ptr peut ne pas posséder de ressource !

Si nous castons un unique_ptr en bool il renvoie true si il possède une ressource (false daans le cas contraire) comme dans l'exemple suivant:

```cpp
class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource détruite\n"; }
    friend ostream& operator<<(ostream& out, const Ressource &res) {
        return out << "Je suis une ressource\n";
    }
};


int main() {
    unique_ptr<Ressource> res(new Ressource);

    if (res){ // cast implicite vers bool pour assurer la possession
             // de ressource 
    cout << *res; // print la ressource possédée par res.
    }

    return 0;
}

```

output:

```
Ressource acquise
Je suis une ressource
Ressource détruite
```

## std::unique_ptr et tableaux

unique_ptr gère très bien le delete de tableaux (delete []), il n'y a aucun problème pour l'utiliser dans ce contexte. Celà dit dans presque tous les cas **il est préférable d'utiliser les classes std::vector ou std::array qu'un smart pointer et un tableau classique !**

### Règle
<span  style="color:red">Favoriser l'utilisation de std::vector, std::array ou std::string sur l'utilisation d'unique_ptr avec un tableau fixe ou dynamique.</span>

## std::make_unique (C++ 14)

C++14 apporte une nouvelle fonction `std::make_unique()`. Cette fonction template construit un objet du type spécifié et l'initialise avec les arguments passé à la fonction.

<span  style="color:red">pas d'initialisation uniforme possible ! </span>

```cpp
class Fraction
{
private:
	int _num = 0;
	int _den = 1;
 
public:
	Fraction(int num = 0, int den = 1): _num(num), _den(den) {}
 
	friend ostream& operator<<(ostream& out, const Fraction &f1) {
		out << f1._num << "/" << f1._den;
		return out;
	}
};
 
 
int main() {
    // crée UNE fraction allouée dynamiquement (3/5)
	unique_ptr<Fraction> f1 = make_unique<Fraction>(3, 5);
	cout << *f1 << '\n';
 
    // crée un tableu dynamique de fractions de longueur 4
    // on peut aussi utiliser auto pour faciliter encore plus
	auto f2 = make_unique<Fraction[]>(4);
	cout << f2[0] << '\n';
 
	return 0;
}
```

output:

```
3/5
0/1
```

L'utilisation de std::make_unique() est optionelle mais à privilégier.  
[plus d'info](http://www.learncpp.com/cpp-tutorial/15-5-stdunique_ptr/)

## unique_ptr en retour de fonction

Aucun soucis de ce coté...

```cpp
unique_ptr<Ressource> creeRessource() {
     return make_unique<Ressource>();
}
 
int main() {
    unique_ptr<Ressource> ptr = creeRessource();
    // fait qqch
 
    return 0;
}
```

Dans ce code, creeRessource() retourne un objet unique_ptr par valeur. Si cette valeur n'est pas attribuée, l'objet t'emporaire se retrouvera hors porté et sera néttoyé ainsi que la ressource qu'il contient. Si assigné, comme dans notre cas, la sémantique de déplacement est utilisée pour le retour. Ce qui en fait un moyen bien plus safe pour retourner des valeurs qu'un pointeur traditionnel.

### Règle
<span  style="color:red">Ne jamais retourner un unique_ptr par référence ou par pointeur à moins d'avoir une raison spécifique de le faire !</span>

## Passer un unique_ptr à une fonction
Si nous voulons passer un un unique_ptr à une fonction sans perdre la possession de l'objet, on la passe par référence <span  style="color:red">const</span>

```cpp
class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource détruite\n"; }

    friend ostream &operator<<(ostream &out, const Ressource &res) {
        return out << "Je suis une ressource\n";
    }
};

void utiliseRessource(const unique_ptr<Ressource> &res) {
    if (res) { cout << *res << endl; }
}



int main() {
    auto ptr = make_unique<Ressource>();
    utiliseRessource(ptr);
    cout << "Fin\n";

    return 0;
    // ressource détruite ici
}

```

output:

```
Ressource acquise
Je suis une ressource

Fin
Ressource détruite
```

Si au contraire, nous voulons que la fonction prènne la possession de l'objet, nous passons le unique_ptr par valeur mais attention à utiliser un `std::move` car la sémentique de déplacement ne joue plus.

```cpp
void prendPossession(unique_ptr<Ressource> res){
    if (res) { cout << *res << endl; }   
} // la ressource est détruite ici

int main() {
    auto ptr = make_unique<Ressource>();
    // prendPossession(ptr) // ne fonctionne pas, on doit forcer
                            // la sémantique de déplacement

    prendPossession(move(ptr)); // ok !

    cout << "Fin\n";
    return 0;
}

```

output:

```
Ressource acquise
Je suis une ressource

Ressource détruite
Fin
```

Bien noter que dans ce cas la posssession a été transfefée à prendPossession() et que la ressource à été détruite à la fin du contexte de la fonction et non pas à la fin du main.

## Mauvais usage d'unique_ptr

Il existe deux façons de chier dans la colle en utilisant unique_ptr. Ces deux façons sont faciles à éviter.

1. Ne pas laisser de multiples classes gérer la même ressource:
```cpp
Ressource *res = new Resource;
unique_ptr<Ressource> res1(res);
unique_ptr<Ressource> res2(res);
```

Même si la syntaxe est légale, le résultat sera que `res1` et `res2` vont tenter de delete la Ressource ce qui est biensur une sitation à éviter!

2. Ne pas delete manuellement la ressource encapsulée dans unique_ptr.

```cpp
Ressource *res = new Ressource;
unique_ptr<Ressource> res1(res);
delete res;
```
La situation est semblable à la première et aura les mêmes conséquences.

<span  style="color:green">A noter que la fonction `std::make_unique()` empèche ces deux situations de se produire  par accident!!</span>


# std::shared_ptr

Contrairement à unique_ptr qui est fait pour ne posséder qu'une ressource, shared\_pointer est fait pour gérer les cas où nous avons besoin de plusieurs smart pointer qui possède la même ressource.

shared_ptr garde de façon interne trace des du nombre d'autres shared\_ptr qui possède une même ressource.

Tant qu'il y a au moins un shared_ptr qui possède une la ressource, cette dernière ne sera pas désaoullée même si d'autres shared\_ptr sont détruits.

Aussitôt que le dernier shared_ptr possèdant la ressource pase hors portée (ou est réassignée ...), la ressource est désaoullée.

```cpp
#include<iostream>
#include<memory>
using namespace std;

class Ressource
{
public:
    Ressource() { cout << "Ressource acquise\n"; }
    ~Ressource() { cout << "Ressource détruite\n"; }
};

int main() {
    // alloue une ressource et la donne au shared_ptr
    Ressource *res = new Ressource;
    shared_ptr<Ressource> ptr1(res);
    {
        shared_ptr<Ressource> ptr2(ptr1); // initialisation par
        // copie du premier shared_ptr
        cout << "Fin de context du premier shared_Ptr\n";
    } // ptr2 sors de portée ici mais rien ne se passe
    
    cout << "Fin de context du second shared_Ptr\n";
    return 0;
} // ptr1 sors de portée ici et les ressources allouées sont
// détruites.
```

output:

```
Ressource acquise
Fin de context du premier shared_Ptr
Fin de context du second shared_Ptr
Ressource détruite
```

Noter qu'on a créé le second shared_ptr en copiant le premier shared\_ptr. <span style="color:red"> Ceci est important ! </span> Considérons le code suivant:

```c++
int main() {
    // alloue une ressource et la donne au shared_ptr
    Ressource *res = new Ressource;
    shared_ptr<Ressource> ptr1(res);
    {
        shared_ptr<Ressource> ptr2(res); // cree ptr2 directement
        // à partir de la ressource (et non pas ptr1);
        cout << "Fin de context du premier shared_Ptr\n";
    } // ptr2 sors de portée ici et les ressources sont détruites
    
    cout << "Fin de context du second shared_Ptr\n";
    return 0;
} // ptr1 sors de portée ici et les ressources allouées sont
// détruites... encore CRASH !
```

Crash !

La différence est ici que nous créons deux shared_ptr indépendant l'un de l'autre. Malgré qu'ils pointent tous les deux sur la même ressource, ils n'en ont pas conscience et quand ptr2 sors de portée, il pense être le seul à posséder cette ressource et la détruit. Une fois que ptr1 sors de portée, il re-détruit les ressources et celà entraine donc un crash.

Cette situation est facile à éviter en gardant en tête d'utiliser un cstr de copie ou un opérateur d'affectation quand nous voulons utiliser plusieurs shared_ptr sur la même ressource.

### Règle

<span style="color:red"> Toujours faire une copie d'un shared_ptr déja éxistant quand on a besoin de plus d'un shared\_pointer pointant sur la même ressource.  </span>

## std::make_shared