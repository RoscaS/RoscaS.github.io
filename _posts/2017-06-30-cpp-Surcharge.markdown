---
layout: post
title: "Cpp: Surcharge"
subtitle:  Surcharge d'opérateurs
date: 2017-06-30
author: Sol
category: Cpp
tags: cpp 
finished: false
---

## 1. Overview

surcharge.hpp
```cpp
#include <iostream>
#include <iomanip>
#include "surcharge.hpp"
using namespace std;

// constructeurs:
// Test::Test() {}
Test::Test(int c) { test_ = c; }
Test::Test(int c, int t) { test_ = c; list_ = new int[t]; }
Test::Test() {
    // toutes les cases de la matrice = 0.0
    for (int row=0; row < 3; ++row)
        for (int col=0; col < 3; ++col)
            matrix_[row][col] = 0.0;
}
// interface:
Test::getTest() const { return test_; }

// surcharges:
//      operateurs arithmetiques        Ces fonctions ne sont pas membres (friend)
Test operator + (const Test& c1, const Test& c2) 
    { return Test(c1.test_ + c2.test_); }

Test operator - (const Test& c1, const Test& c2) 
    { return Test(c1.test_ - c2.test_); }

Test operator * (const Test& c1, const Test& c2) 
    { return Test(c1.test_ * c2.test_); }

Test operator / (const Test& c1, const Test& c2) 
    { return Test(c1.test_ / c2.test_); }

//      operateurs de comparaison       Ces fonctions ne sont pas membres (friend)
bool operator == (const Test &c1, const Test &c2) 
    { return c1.test_ == c2.test_; }

bool operator != (const Test &c1, const Test &c2) 
    { return c1.test_ != c2.test_; }

bool operator >  (const Test &c1, const Test &c2) 
    { return c1.test_ >  c2.test_; }

bool operator <= (const Test &c1, const Test &c2) 
    { return c1.test_ <= c2.test_; }

bool operator <  (const Test &c1, const Test &c2) 
    { return c1.test_ <  c2.test_; }

bool operator >= (const Test &c1, const Test &c2) 
    { return c1.test_ >= c2.test_; }

//      operateurs I/O                  Ces fonctions ne sont pas membres (friend)
ostream& operator << (std::ostream& out, const Test& c) 
    { return out << "Centimes: " << c.test_; }

istream& operator >> (std::istream& in , Test& c) 
    { in >> c.test_; } // voir Fraction !!!

//      operateurs unaires              Fonctions membre
Test Test::operator - () const 
    { return Test(-test_); }

Test Test::operator + () const 
    { return *this; }

bool Test::operator ! () const 
    { return (test_ == 0); }

//      operateurs inc/dec pre/postfix  Fonctions membre
Test& Test::operator ++ () 
    { ++test_; return *this; } // prefix

Test& Test::operator -- () 
    { --test_; return *this; } // prefix

Test  Test::operator ++ (int) 
    { Test temp(test_); ++(*this); return temp; } // postfix

Test  Test::operator -- (int) 
    { Test temp(test_); --(*this); return temp; } // postfix

//      operateur de subscription []    Fonction membre
int& Test::operator[] (const int index) 
    {return list_[index]; }

//      parentheses
double& Test::operator()(int row, int col) 
    {return matrix_[row][col]; }

const double& Test::operator()(int row, int col) const 
    {return matrix_[row][col]; }




// OPPERATEURS += et -=     voir classe bitArr !!!

// Bit& operator+=(const int& in){
//       this->m_iNumber += rhs.m_iNumber;
//       return *this;
// }
```


surcharge.cpp
```cpp
#pragma once
#include <iostream>

class Test
{
private:
    int test_;
    int* list_;
    double matrix_[4][4];
public:
    // constructeurs
    Test();
    Test(int);
    Test(int,int);
    Test(int,int,int);
    // interface
    int getTest() const;
    // surcharges
    //      operateurs arithmetiques (binaire: 2 operandes)
    friend Test operator + (const Test& c1, const Test& c2);
    friend Test operator - (const Test& c1, const Test& c2);
    friend Test operator * (const Test& c1, const Test& c2);
    friend Test operator / (const Test& c1, const Test& c2);
    //      operateurs de comparaison (binaire: 2 operandes)
    friend bool operator == (const Test &c1, const Test &c2);
    friend bool operator != (const Test &c1, const Test &c2);
    friend bool operator >  (const Test &c1, const Test &c2);
    friend bool operator <= (const Test &c1, const Test &c2);
    friend bool operator <  (const Test &c1, const Test &c2);
    friend bool operator >= (const Test &c1, const Test &c2);
    //      operateurs I/O (binaire: 2 operandes)
    friend std::ostream& operator << (std::ostream& out, const Test& c);
    friend std::istream& operator >> (std::istream& in , Test& c);
    //      operateurs unaires (unaire: 1 operande)
    Test operator - () const;
    Test operator + () const;
    bool  operator ! () const; // retourne true si Test = 0
    //      operateurs inc/dec pre/postfix (unaire: 1 operande)
    Test& operator ++ (); // prefix
    Test& operator -- (); // prefix
    Test  operator ++ (int); // postfix
    Test  operator -- (int); // postfix
    //      operateur de subscription []
    int& operator [] (const int index);
    //      parentheses
    double& operator()(int row, int col);
    const double& operator()(int row, int col) const; // for const objects

};
```


