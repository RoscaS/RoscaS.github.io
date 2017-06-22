---
layout: post
title: "Cpp: Semantique de copie (rappel)"
subtitle: 
date: 2017-06-21
author: Sol
category: Cpp
tags: cpp 
finished: false
---

## Rappel: types d'initialisation:

```cpp
class Fraction {
private:
    int m_num; int m_den;
public:
    Fraction(int num = 0, int den = 1) {
        m_num = num; m_den = den;
    }
    friend ostream& operator <<
        (ostream& out, const Fraction& f) {
        return out << f.m_num << "/" << f.m_den;
    }
};
```
### Initialisation **$$\large directe$$**
* `int x(5);` initialisation directe d'un objet de type `int`
* `Fraction f1(5,4);` initiasation directe d'un objet de type `Fraction`
    * $$ \Rightarrow $$ appelle le cstr de `Fraction(int,int)`

### Initialisation **$$\large uniforme$$**
* `int y{5};` initialisation uniforme d'un objet de type `int`
* `Fraction f2{5,3};`  initialisation uniforme d'un objet de type 'Fraction`
    * $$ \Rightarrow $$ appelle le cstr de `Fraction(int,int)`

### Initialisation par **$$\large copie$$**
* `int z = 6;` initialisation par copie d'un objet de type `int`
* `Fraction f3 = Fraction(6);` initialisation par copie d'un objet de type `Fraction`
    * $$\Rightarrow$$ appelle le constructeur `Fraction(6,1)` **ou** le cstr de copie. Ce dernier n'est généralement pas le premier choix du compilateur pour raison de performance.
    * Cette façon de faire est évaluée de la même façon que le serait `Fractionf3(fraction(6));`
* `Fraction f4 = 7;` initialisation par copie d'un objet de type `Fraction`.
    * $$\Rightarrow$$ le compilateur va tenter de trouver une façon de convertir 7 en un objet de type `Fraction` ce qui va invoquer le cstr `Fraction(7,1)`

<br>

> Autre cas d'initialisation par copie:
>* Passage d'une classe (objet de type...) comme argument
>* Retour d'une classe (objet de type...)
> 

<br>

### Règle
<span style="color:red">**Éviter l'initialisation par copie au profit de de l'initialisation uniforme**</span>

## Rappel: Opérateur d'affectation et cstr de copie
Le constructeur de copie et l'opérateur d'assignation sont presque équivalents, les deux copient un objet dans un autre.

#### Opérateur d'affectation
Remplace le contenu d'objets préexistants (comme c'est une affectation, il assigne une valeur à un objet qui existe déja).

 ```cpp
 int b;  // initialisation (contient du garbage)
 b = 5;  // affectation à un objet préexistant
 ```

Il est évident qu'on ne peut pas faire `b = 5;` sans avoir fait au préalable l'initialisation `int b;`. Dans la même logique: `f1 = (5,2);` doit être précédé de `Fraction f1;`

#### Constructeur de copies
Initialise de nouveaux objets. 


### Règle
 * <span style="color:red">si un nouvel objet doit être créé avant qu'une copie puisse se faire le cstr de copies est utilisé (inclut le passage d'objet en argument (par valeur) et le retour d'objets par valeur).</span>

* <span style="color:red">si un nouvel objet ne doit pas forcément être créé avant qu'une copie puisse se passer, on utilise l'opérateur d'affectation.</span>