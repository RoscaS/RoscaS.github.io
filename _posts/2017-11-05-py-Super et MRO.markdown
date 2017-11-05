---
layout: post
title: "Py: Methode Super"
subtitle: ""
date: 2017-11-05
author: Sol
category: Py
tags: tools
finished: true
mathjax: true
---

## Source

* [Python 201: intermediat Python](https://www.blog.pythonlibrary.org/2017/03/08/python-201-is-now-an-online-course/)

## Super

La fonction built-in `super` retourne un "proxy" objet qui va déléguer les appels de méthode à une classe parent. 

Autrement dit, cette fonction nous permet d'accéder à des méthodes héritées tel qu'elle le sont dans la classe parent.

> On note qu'en Python 3 il n'est plus nécéssaire de dire à Python qu'une classe hérite de la classe de base `Object`. Python le fait implicitement si aucun héritage n'est spécifié.

```py
class A():
    def __init__(self):
        pass
    
    def display(self):
        print('A')


class B(A):
    def __init__(self):
        pass

    def display(self):
        print('B')


b = B()

b.display()

super(B, b).display()
```
La syntaxe est: `super(Classe, objet)[.méthode][.variable de classe]` et retourne dans le cas de notre exemple:

```
B
A
```

Un second exemple plus complet:

```py
class A():
    def __init__(self, arg1):
        self.arg1 = str(arg1)
    
    def display(self):
        return self.arg1


class B(A):
    def __init__(self, arg1, arg2):
        super().__init__(arg1)
        self.arg2 = str(arg2)

    def display(self):
        return super().display() + self.arg2


class C(B):
    def __init__(self, arg1, arg2, arg3):
        super().__init__(arg1, arg2)
        self.arg3 = str(arg3)

    def display(self):
        return super().display() + self.arg3


a = A(1)
b = B(1,2)
c = C(1,2,3)


print(a.display())
print(b.display())
print(c.display())
```

Qui retourne:

```
1
12
123
```

La méthode super à deux cas d'utilisation classiques:
1. Héritage simple: `super` est utilisé pour faire référence à la classe parent sans la nommer explicitement. C'est une mécanique répandue dans les langages orienté objet.

2. Le second cas est plus complexe car contrairement au premier il est unique à Python. Il permet, dans un environement d'execution dynamique le support d'héritage multiple coopératif. Cette mécanique ne peut pas se retrouver dans un langage qui ne supporte pas l'héritage multiple ou dans les langages compilés statiques (C++, ...).


## MRO (Method Resolution Order)

La méthode `__mro__` retourne un tuple contenant les classes dont l'objet appelant est dérivé. Si nous avons une clase qui hérite de deux autres classes, on pourrait penser que son MRO sera: La classe elle-même ainsi que les deux parents dont elle hérite mais li ne faut pas oublier qu'en Python, les classes parents sont elles-mêmes dérivées de la classe de base `Object`:

```py
class X():
    def __init__(self):
        print('X')
        super().__init__()

class Y():
    def __init__(self):
        print('Y')
        super().__init__()

class Z(X, Y):
    pass

z = Z()
print(Z.__mro__)
```

Ici nous créons 3 classes. Les deux premières ne font que printer leur nom et la dernière hérite des deux premières. Ensuite on instancie un objet de type `Z` et printons son MRO qui retourne:

```
X
Y
(<class '__main__.Z'>, <class '__main__.X'>, <class '__main__.Y'>, <class 'object'>)
```

Comme nous pouvons le voir, lors de l'instanciation de l'objet de type `Z`, les deux classes parents executent le code dans leur `__init__`, ensuite nous avons la méthode `__mro__` appelée par l'objet de type `Z` qui nous retourne une liste qui contient l'ordre de résolution que suit un objet de son type qui est:  `Z` ensuite `X` puis `Y` et finalement `Object`.

Un autre exemple parlant est de regarder ce qui se passe quand on crée une variable de classe dans la classe de base et qu'on l'override plus tard:

```py
class Base:
    var = 5
    def __init__(self):
        pass

class X(Base):
    def __init__(self):
        print('X')
        super().__init__()

class Y(Base):
    var = 10
    def __init__(sefl):
        print('Y')
        super().__init__()

class Z(X, Y):
    pass


z = Z()
print(Z.__mro__)
print(super(Z, z).var)
```

Ici nous créons une classe `Base` avec un variable de classe qui vaut `5`. Ensuite nous créons les sous-classes `X` et `Y` qui héritent de `Base`. La sous-classe `Y` override la variable de class `var` et lui affecte la valeur `10`. Finallement nous créons la sous-classe `Z` qui hérite de `X` **et** `Y`. Quand nous appellons la fonction `super` sur la classe `Z`, quelle variable de classe sera printée?

```
X
Y
(<class '__main__.Z'>, <class '__main__.X'>, <class '__main__.Y'>, <class '_
_main__.Base'>, <class 'object'>)
10
```

La classe `Z` hérite de `X` et `Y`, donc quand on lui demande que vaut `var`, son MRO regarde en premier dans `X` pour voir si `var` y est définie. Comme ce n'est pas le cas, le MRO regarde dans `Y` et y trouve une assignation pour la variable `var` qui sera donc celle utilisée.

Si nous ajoutons une assignation pour la variable `var` dans le corp de la classe `X` c'est celle qui sera utilisée car elle apparait en premier dans le MRO de la classe `Z`:

```py
class Base:
    var = 5
    def __init__(self):
        pass
    
class X(Base):
    var = 100
    def __init__(self):
        print('X')
        super().__init__()

class Y(Base):
    var = 10
    def __init__(sefl):
        print('Y')
        super().__init__()

class Z(X, Y):
    pass


z = Z()
print(Z.__mro__)
print(super(Z, z).var)
```

Et retournera donc:

```
X
Y
(<class '__main__.Z'>, <class '__main__.X'>, <class '__main__.Y'>, <class '_
_main__.Base'>, <class 'object'>)
100
```

> Le MRO dépend de l'ordre d'écriture des classes dont hérite une nouvelle classe.

La fonction `super` sait donc comment interpréter le MRO et stock ces informations dans les propriétés magiques `__thisclass__` et `__self_class__`:

```py
class Base():
    def __init__(self):
        s = super()
        print(s.__thisclass__)
        print(s.__self_class__)
        s.__init__()

class SubClass(Base):
    pass

sub = SubClass()
```

Ici nous créons une classe `Base` et lui assignons l'appel de la fonction `super` à une variable `s` pour que nous puissions voir ce que les propriétés `__thisclass__` et `__self_class__` contiennent. Ensuite nous les printons et faisons l'initialisation. Finallement, on instancie un objet de type `SubClass()` qu'on assigne à la variable `sub`. Output:

```
<class '__main__.Base'>
<class '__main__.SubClass'>
```

Tout ceci est bon à savoir mais est particulièrement utile lors de la définition de métaclasses ou quand on veut mélanger des classes.

## Conclusion

Il y a un tas de façons interessantes d'utiliser la fonction `surper` qu'on peut trouver sur le net. La majorité sont à première vue assez "wtf" mais une fois qu'on a bien compris, leur fonctionnement cela peut généralement se révélé assez impressionnant et pratique.

> Personally I haven’t had a need for super in most of my work, but it can be useful for forward compatibility. What this means is that you want your code to work in the future with as few changes as possible. So you might start off with single inheritance today, but a year or two down the road, you might add another base class. If you use super correctly, then this will be a lot easier to add. I have also seen arguments for using super for dependency injection, but I haven’t seen any good, concrete examples of this latter use case. It’s a good thing to keep in mind though. 

