---
layout: post
title: "Cpp: R-value et L-value"
subtitle: "plus particulièrement: Références sur les R-value"
date: 2017-06-21
author: Sol
category: Cpp
tags: cpp 
finished: false
---

> Il existe d'autres -values que les deux présentés ici. Pour plus d'info: [cppreference](http://en.cppreference.com/w/cpp/language/value_category)

Malgré la présence du mot "valeur" dans le nom **l-value **et **r-value** ne sont en réalité pas des valeurs mais plutôt des propriétés des expressions.

Toute expression en C++ à **deux propriétés**:
1. Un **type**: Utilisé pour le typage et la vérification)
2.  Une **catégorie de valeur**: Utilisé pour certains types de vérification syntaxique comme "est-ce que le résultat de cette expression peut être assigné ici"

_La défintion formelle de ce qu'est une L-value et une R-value est largement audessus de ma compréhension actuelle et je vais me contenter d'expliquer dans les grandes lignes ce qui à mon niveau est important._

#### L-value

Il est plus facile de penser à une L-value (également appelé "locator value") comme à une fonction ou un objet (ou une expression qui s'évalue comme une fonction ou un objet). **Toutes** les L-values se voient assigné une adresse en mémoire.

Initialement dans les vielles version de C++, les L-values étaient définies comme "valeurs qui peuvent se retrouver à gauche de l'opérateur d'assignation". Celà dit, depuis l'introduction du mot clé `const`, les l-values se sont fait diviser en deux sous-catégories:

* L_value **modifiables** 
* L_value **non-modifiables**


#### R-value

Il est plus simple de penser à une R-value comme étant _tout ce qui n'est pas une L-value_. Celà inclue:

* "literals" (ex. `5`)
* valeurs temporaires (ex. `x+1`)
* objets anonymes (ex. `Fraction(5,2)`)

Les r-values sont typiquement:

* évaluées pour leur valeurs
* ont une portée
* on ne peut pas leur assigner quelque chose

Cette règle de non assignement possible 

## référence à une L-value

On ne peut référencer une L-value qu'avec des L-value **modifiables**:

|reference L-value | peut être init avec | peut modifier |
|:--:|:--:|:--:|
|L-value modifiable|oui|oui|
|L-value non-modifiable|non|non|
|R-value|non|non|


Les références L-value à des objets `const` peuvent être initialisées indiférament avec des L_values ou des R-values cependant ces valeurs ne peuvent être modifiés:

|reference L-value à une `const` | peut être init avec | peut modifier |
|:--:|:--:|:--:|
|L-value modifiable|oui|non|
|L-value non-modifiable|oui|non|
|R-value|oui|non|

>Les références sur des objets `const` sont particulièrement utiles car elles nous permettent de passer n'importe quel type d'argument (L-value ou R-value) dans une fonction sans faire de copie de l'argument.


## référence à une R-value

Une référence R-value est une référence faite pour être uniquement initialisée avec une R-value. 

> Une référence sur une L-value est faite avec une esperluette `&`, une référence sur une R-value se fait avec 2 esperluettes `&&`.

```c++
int x = 5;
int &lref = x; // référence L-value initialisée avec L-value x
int &&rref = 5; // référence R-value initialisée avec R-value 5
```

Les références R-value ne peuvent être initialisées avec des L-value:

|reference R-value | peut être init avec | peut modifier |
|:--:|:--:|:--:|
|L-value modifiable|non|non|
|L-value non-modifiable|non|non|
|R-value|oui|oui|

<br>

|reference R-value à une `const` | peut être init avec | peut modifier |
|:--:|:--:|:--:|
|L-value modifiable|non|non|
|L-value non-modifiable|non|non|
|R-value|oui|non|

Les références R-value ont deux propriétés qui nous interesse pour le moment:

1. Elles étendent la durée de vie (lifespan) de l'objet qui les initialise avec la durée de vie (lifespan) de la référence R-value.
>Les références L_value à des objets `const` fait la même chose mais c'est bien plus utilie pour les références R-value à cause de la portée des expressions des R-value.
2. Les références R-value non `cont`nous permettent de modifier la R-value.

----
Quelles lignes ne vont pas compiler?

``` cpp
int main() {
	int x;
 
	// l-value references
	int &ref1 = x; // A
	int &ref2 = 5; // B
 
	const int &ref3 = x; // C
	const int &ref4 = 5; // D
 
	// r-value references
	int &&ref5 = x; // E
	int &&ref6 = 5; // F
 
	const int &&ref7 = x; // G
	const int &&ref8 = 5; // H
	
	return 0;
}
```

Si nous suivons les indications des tableaux précédent on peut identfier que les cas: `B`, `E` et `G` ne compileront pas.

## Exemples:

```cpp
#include<iostream>
using namespace std;

class Fraction
{
private:
    int _num;
    int _den;
public:
    Fraction(int num=0, int den=1): _num(num), _den(den) {}
    friend ostream &operator<<(ostream &out, const Fraction &f1) {
        out << f1._num << "/" << f1._den;
        return out;
    }
};


int main() {
    Fraction &&rref = Fraction(3,5); // reference R-value à une fraction anonyme
    cout << rref << "\n";

    return 0;
    // fin de portée de rref et Fraction anonyme
}

// output:
// 3/5
```

En tant qu'objet anonyme, Fraction(3,5) devrait normalement se retrouver hors contexte à la fin de l'expression dans laquelle elle est définie mais comme on la référence par une R-value, sa durée de vie est étendue jusqu'à la fin du bloc. 

Voyons maintenant un exemple moins intuitif:

```cpp
int main() {
    int &&rref = 5; // Comme nous initialisons une référence R-value à une expression literale
    // un "temporaire" avec une valeur de 5 est créé ici.

    rref = 10;
    cout << rref << "\n";
    
    return 0;
}

// output:
// 10
```

Il peut sembler étrange d'initialiser une référence R-value avec une expression literale et ensuite pouvoir changer cette valeur. Ce qu'il se passe quand on initialise une R-value avec une expression litérale, c'est qu'un **temporaire** est créé à partir de l'expression litérale pour que la référence puisse référencer un objet (temporaire) et non une expression litérale.

> Les références R-value **ne sont pas** souvent utilisées comme dans ces deux exemples.

## référence R-value comme paramètre de fonction

Utilité principale des références R-value. Très utile pour la surcharge de fonctions quand on veut avoir des comportements différents pour les arguments L-value et R-value.

```cpp
void func(const int &lref) { // argument L-value choisira cette fonction
    cout << "reference L-value a const\n";
}

void func(int &&lref) { // argument R-value choisira cette fonction
    cout << "reference R-value\n";
}

int main() {
    int x = 5;
    func(x); // argument L-value appel la version L-value de func()
    func(5); // argument R-value appel la version R-value de func()

    return 0;
}

// output:

// reference L-value a const
// reference R-value
```

## Pourquoi ?

Ces mécanismes sont importants pour pouvoir comprendre la [**sémantique de déplacement**](\cpp\cpp-move&smartP.html)

## Important

**On ne devrait presque jamais retourner une référence sur une R-value pour la même raison qu'on ne devrait presque jamais retourner une référence L-value!** Si on le fait, dans la majorité des cas, la valeur de retour sera une référence pointant sur un objet qui est sorti de son contexte à la fin de la fonction!

