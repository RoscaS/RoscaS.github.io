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
