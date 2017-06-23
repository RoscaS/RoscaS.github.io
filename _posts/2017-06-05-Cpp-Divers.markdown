---
layout: post
title: "Cpp: Divers"
subtitle: "Diverses notions utiles"
date: 2017-06-05
author: Sol
category: Cpp
tags: Cpp
finished: false
---

## Uniform initialization syntax
[Vittorio Romeo](https://www.youtube.com/watch?v=tPbrWAbzyTE)  

```cpp
#include<iostream>

struct Exemple
{
    Exemple(int x)
    {
        std::cout << "Constructor\n"; 
    }
    Exemple& operator=(int X)
    {
        std::cout << "Operator=\n";
        return *this;
    }
};


int main()
{
    // Meme si celà ressemble à une affectation,
    // ce n'est pas le cas. C'est une déclaration
    // et donc nous appelons ici le constructeur
    // Exemple::Exemple(int) qui print "Construcor".
    Exemple exemple1 = 10;

    // Ceci est une affectation et print donc "Operator=",
    exemple1 = 5;

    
    // Utilisation de la syntaxe d'initialisation uniforme:

    // Ici est est clair que nous appelons le constructeur
    Exemple exemple2{10};

    // exemple2{20}; <= erreur à compilation. Nous ne pouvons
    // pas affecter de valeur de cette façon.

    // Il est maintenant clair que ceci est une affectation
    exemple2 = 10;

    // La synyaxe devient uniforme parenthèses pour les fonctions
    // acolades pour les classes. Exemple avec un simple agrégat:
    struct Point { int x, y; };

    // Initialisation et affectation d'une instance:
    Point a{2,4};
    
    return 0;
}
```

Autre exemple:

```cpp
struct Vector2 { float x, y; };

// Ces deux fonctions sont équivalentes
// en utilisant la syntaxe { ... } nous économisons
// quelques frapes:

Vector2 getMyVector1() { return Vector2( 5.f, 5.f); }
Vector2 getMyVector2() { return { 5.f, 5.f}; }

// Note importante:
// Si nous utilisons 'auto' pour laisser le compilateur
// déduire le type, NE JAMAIS utiliser la syntaxe '{ ... }'
// Cette façon de faire dans ce contexte est la façon de 
// déclarer une 'std::initializer list'. Quand nous utilisons
// 'auto' il faut toujours utiliser l'initialisation l'initialisation
// directe ( ... ).

auto x = 5; // (ok, x est un 'int')
auto x(5);  // (ok, x est un 'int')
auto x{5};  // (nok, x est un 'std::initializer_list<int>')
```

Vraiment pratique:

```cpp
int main() {
        int a{1}, b{2}, c{3};
        float d{10.f}, e{13.f}
}
```




## What is the difference between `printf()` and `cout`
[Quora](https://www.quora.com/What-is-the-difference-between-printf-cout-in-C++)

`printf` is a function and `cout` is a global (namespaced) variable.

```c++
cout << "Hello world" << endl
```

In this example, You are actually making two function calls, both of them to overloads of `operator<<()`. This function is a binary operator; the first parameter is of type `std::ostream&` (an output stream) and the second parameter is of any type that can be output. It returns its first parameter, allowing this operator to be chained for outputting multiple values.

Unlike `printf`, cout (and other output streams) are type safe. When you do something like `printf("%d",x);`, `printf` will treat `x` as an integer, no matter what type it actually is. This can be disastrously unsafe if you make a mistake. With `cout`, the types are all inferred at compile time, and there is no way to make this kind of mistake.

On the other hand, complex formatting is much easier to do with `printf`. You can format `cout`, but it is much **more verbose**, with lots of insertion of strange objects with names like **precision** and **hex**. Even then it doesn't have quite as many options as `printf`. Using `cout` is usually more verbose in general, even without formatting. So even though `cout` can do much of the formatting that `printf` can do, `printf` has hung onto the "best for easy formatting" niche, even in C++ code.


### Complexity

```c++
#include <cstdio>
int main()
{
    printf("string %d", nb);
}
```

```c++
#include <iostream>
int main()
{
    std::cout << a; 
}
```

`printf()` works faster than `cout`, 
* `printf()` is given the  type of the veriables to print. 
* `cout` first have to find the type and then print 

> Additionally `printf()` statements are shorter to write.


## Tester la casse d'un char

![img](https://i.stack.imgur.com/vDNf8.gif)

```c++
#include<iostream>
#include<cstring>
using namespace std;

int main()
{
    string s;
    cin >> s;   
    for (int i = 0; i < 100; ++i) {
        if (s[i] <= 90) {
            s[i] = s[i] + 32;
        }
        else {
            s[i] = s[i] - 32;
        }
    }
    
    cout << s << endl;
    return 0;
}
```

## String len

```c++
char *a = "poule";
cout << strlen(a) << endl;

string b = "poule";
cout << b.size() << endl;
```

## Spaces in user input

```c++
using namespace std;


int main() {
    int i = 4;
    double d = 4.0;
    string s = "HackerRank ";

    int I;
    double D;
    string S;

    cin >> I;
    cin >> D;
    getline(cin >> ws, S);

    printf("%d\n%.1f\n", i+I, d+D);
    cout << s+S << endl;
    return 0;
}
```