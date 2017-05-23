---
layout: post
title:  "Reseau: labo 5"
date:   2017-05-21
categories: Res
---

# Exception handling
When an exception is raised (using `throw`), execution of the program immediately jumps to the nearest enclosing `try` block (propagating up the stack if necessary to find an enclosing `try` block. 
If any of the `catch` handlers attached to the `try` block handle that type of exception, that handler is executed and the exception is considered handled.

If no appropriate catch handlers exist, execution of the program propagates to the next enclosing `try` block. If no appropriate catch `handlers` can be found before the end of the program, **the program will fail with an exception error**.

```c++
// 00intro.cpp

int main()
{
    cout << "Enter a number" << "\n";
    double x;
    cin >> x;

    try
    {
        if(x < 0.0)
        {
            throw static_cast<string>("Can't take sqrt of neg nb");
        }
        cout << "the sqrt of " << x << " is "
             << sqrt(x) << endl;
    }
    
    catch (const string exception)
    {
        cerr << "Error: " << exception << endl;
    }

    return 0;
}
```

I have some issues with my compiler that prints trash char if I use 

```C++
if(x < 0.0)
{
    throw ("Can't take sqrt of neg nb");
}

/// ...

catch (const char* exception)
{
    std::cerr << "Error: " << exception << std::endl;
}

```

That's why I use a `static_cast`.


## What catch blocks typically do

If an exception is routed to a catch block, it is considered “handled” even if the catch block is empty. However, typically you’ll want your catch blocks to do something useful. There are three common things that catch blocks do when they catch an exception:

1. catch blocks may print an error (either to the console, or a log file).

2. catch blocks may return a value or error code back to the caller.

3. a catch block may throw another exception. Because the catch block is outside of the try block, the newly thrown exception in this case is not handled by the preceding try block -- it’s handled by the next enclosing try block.

## Exceptions and functions

```c++
double mySqrt(double x)
{
    if (x < 0.0)
    {
        throw static_cast<string>("Can't take sqrt of neg nb");
    }
    return sqrt(x);
}


int main()
{
    cout << "Enter a number" << "\n";
    double x;
    x = -4;

    try
    {
        cout << "the sqrt of " << x << " is "
             << mySqrt(x) << endl;
    }
    catch (const string exception)
    {
        cerr << "Error: " << exception << endl;
    }
    
    return 0;
}
```
output:

`Error: Can't take sqrt of neg nb`


Let’s revisit for a moment what happens when an exception is raised. 
* First, the program looks to see if the exception can be handled immediately (which means it was thrown inside a try block). 
* If not, the current function is terminated, and the program checks to see if the function’s caller will handle the exception. 
* If not, it terminates the caller and checks the caller’s caller. 
* Each function is terminated in sequence until a handler for the exception is found, or until main() is terminated without the exception being handled. 

This process is called **unwinding the stack**.

The most interesting part of the above program is that the mySqrt() function can throw an exception, but this exception is not immediately inside of a try block! This essentially means mySqrt is willing to say, “Hey, there’s a problem!”, but is unwilling to handle the problem itself.

> Different applications may want to handle errors in different ways. A console application may want to print a text message. A windows application may want to pop up an error dialog. In one application, this may be a fatal error, and in another application it may not be. By passing the error back up the stack, each application can handle an error from mySqrt() in a way that is the most context appropriate for it! Ultimately, this keeps mySqrt() as modular as possible, and the error handling can be placed in the less-modular parts of the code.

## catch-all

Using the catch-all handler to wrap main()

One interesting use for the catch-all handler is to wrap the contents of main():

```c++
#include <iostream>
int main()
{
 
    try
    {
        runGame();
    }
    catch(...)
    {
        std::cerr << "Abnormal termination\n";
    }
 
    saveState(); // Save user's game
    return 1;
}
```

In this case, if runGame() or any of the functions it calls throws an exception that is not caught, that exception will unwind up the stack and eventually get caught by this catch-all handler. This will prevent main() from terminating, and gives us a chance to print an error of our choosing and then save the user’s state before exiting. This can be useful to catch and handle problems that may be unanticipated.


## Exception specifiers

**This subsection should be considered optional reading because exception specifiers are rarely used in practice, are not well supported by compilers, and Bjarne Stroustrup (the creator of C++) considers them a failed experiment.**

Exception specifiers are a mechanism that allows us to use a function declaration to specify whether a function may or will not throw exceptions. This can be useful in determining whether a function call needs to be put inside a try block or not.

There are three types of exception specifiers, all of which use what is called the throw (…) syntax.

First, we can use an empty throw statement to denote that a function does not throw any exceptions outside of itself:

```c++
int doSomething() throw(); // does not throw exceptions
```
Note that `doSomething()` can still use exceptions as long as they are handled internally. Any function that is declared with `throw()` is supposed to cause the program to terminate immediately if it does try to throw an exception outside of itself, but implementation is spotty.

Second, we can use a specific throw statement to denote that a function may throw a particular type of exception:

```c++
int doSomething() throw(double); // may throw a double
```

Finally, we can use a catch-all throw statement to denote that a function may throw an unspecified type of exception:

```c++
int doSomething() throw(...); // may throw anything
```

Due to the incomplete compiler implementation, the fact that exception specifiers are more like statements of intent than guarantees, some incompatibility with template functions, and the fact that most C++ programmers are unaware of their existence, I recommend you do not bother using exception 

## Exceptions and member functions

## Overloaded operators

Overloaded operators have specific requirements as to the number and type of parameter(s) they can take and return, there is no flexibility for passing back error codes or boolean values to the caller. However, since exceptions do not change the signature of a function, they can be put to great use here. Here’s an example:

```c++
int& IntArray::operator[](const int index)
{
    if (index < 0 || index >= getLength())
    {
        throw index;
    }

    return m_data[index];
}
```
Now, if the user passes in an invalid index, operator[] will throw an int exception.

## Constructors

Constructors are another area of classes in which exceptions can be very useful. If a constructor fails, simply throw an exception to indicate the object failed to create. **The object’s construction is aborted** and its **destructor is never executed** (note: this means your exception handler should **handle any necessary cleanup**).


## Exception class
An exception class is just a normal class that is designed specifically to be thrown as an exception.

```c++
class ArrayException
{
private:
    std::string _error;
public:
    ArrayException(std::string error)
        : _error(error) {}
    
    const char* getError() { return _error.c_str(); }
};

class IntArray
{
protected:
    int _data[3];
    
public:
    IntArray(){}
    int getLenght() { return 3; }
    int& operator[](const int index)
    {
        if (index < 0 || index >= getLenght())
        {
            throw ArrayException("Invalid index");
        }

        return _data[index];
    }
};

int main()
{
    IntArray a1;

    try
    {
        int value = a1[5];
    }
    catch (ArrayException &exception)
    {
        std::cerr << "An array exception occured (" << exception.getError() << ")\n";
    }
    
    return 0;
}
```

Using such a class, we can have the exception return a description of the problem that occurred, which provides context for what went wrong. And since ArrayException is its own unique type, we can specifically catch exceptions thrown by the array class and treat them differently from other exceptions if we wish.

Note that exception handlers should catch class exception objects by **reference** instead of by value. This prevents the compiler from making a copy of the exception, which can be **expensive** when the exception is a class object, and **prevents object slicing** when dealing with derived exception classes. Catching exceptions by **pointer should generally be avoided** unless you have a specific reason to do so.

## Exceptions and inheritance

```c++
class Base
{
public:
    Base() {}
};
 
class Derived: public Base
{
public:
    Derived() {}
};
 
int main()
{
    try
    {
        throw Derived();
    }
    catch (Base &base)
    {
        cerr << "caught Base";
    }
    catch (Derived &derived)
    {
        cerr << "caught Derived";
    }
 
    return 0;
}	
```

output:  
```
caught Base
```

Derived classes will be caught by handlers for the base type. Because Derived is derived from Base, Derived is-a Base (they have an is-a relationship). Second, when C++ is attempting to find a handler for a raised exception, it does so **sequentially**. Consequently, the first thing C++ does is check whether the exception handler for Base matches the Derived exception. Because Derived is-a Base, the answer is yes, and it executes the catch block for type Base! The catch block for Derived is never even tested in this case.

In order to make this example work as expected, we need to flip the order of the catch blocks:

```c++
class Base
{
public:
    Base() {}
};
 
class Derived: public Base
{
public:
    Derived() {}
};
 
int main()
{
    try
    {
        throw Derived();
    }
    catch (Derived &derived)
    {
        cerr << "caught Derived";
    }
    catch (Base &base)
    {
        cerr << "caught Base";
    }
 
    return 0;
}	
```
This way, the Derived handler will get first shot at catching objects of type Derived (before the handler for Base can). Objects of type Base will not match the Derived handler (Derived is-a Base, but Base is not a Derived), and thus will “fall through” to the Base handler.

>**Rule: Handlers for derived exception classes should be listed before those for base classes.**

>The ability to use a handler to catch exceptions of derived types using a handler for the base class turns out to be exceedingly useful.

## std::exception

Many of the classes and operators in the standard library throw exceptions classes on failure. For example, operator new and std::string can throw std::bad\_alloc if they are unable to allocate enough memory. A failed dynamic\_cast will throw std::bad_cast.

All of these exception classes are derived from a single class called std::exception. std::exception is a small interface class designed to serve as a base class to any exception thrown by the C++ standard library.

>Thanks to std::exception, we can set up an exception handler to catch exceptions of type std::exception, and we’ll end up catching std::exception and all (21+) of the derived exceptions together in one place. Easy!

```c++
#include<iostream>
#include<exception>
#include<string>

int main()
{
    try
    {
        // code with standard library goes here
        // We'll trigger one of these exception...
        std::string s;
        s.resize(-1); // will trigger a std::bad_alloc
    }
    // this handler will catch std::exception and all the
    // ferived exceptions too
    catch (std::exception &exception)
    {
        std::cerr << "Standard exception: " << exception.what() << '\n';
    }

    return 0;
}
```
The above program prints:
```
Standard exception: string too long
```

`std::exception` has a virtual member function named `what()` that returns a C-style string description of the exception. Most derived classes override the `what()` function to change the message. Note that this string is meant to be used for descriptive text only -- **do not use it for comparisons**, as it is not guaranteed to be the same across compilers.

Sometimes we’ll want to handle a specific type of exception differently. In this case, we can add a handler for that specific type, and let all the others “fall through” to the base handler. Consider:

```c++
try
{
    
}
// This handler will catch std::bad_alloc 
// (and any exceptions derived from it) here
catch (std::bad_alloc &exception)
{
    std::cerr << "You ran out of memory! " << '\n';
}
catch (std::exception &exception)
{
    std::cerr << "Standard exception: " << exception.what() << '\n';
}
```
In this example, exceptions of type std::bad_alloc will be caught by the first handler and handled there. Exceptions of type std::exception and all of the other derived classes will be caught by the second handler.

Such inheritance hierarchies allow us to use specific handlers to target specific derived exception classes, or to use base class handlers to catch the whole hierarchy of exceptions. This allows us a fine degree of control over what kind of exceptions we want to handle while ensuring we don’t have to do too much work to catch “everything else” in a hierarchy.

## Using the standard exceptions directly

Nothing throws a std::exception directly, and neither should you. However, you should feel free to throw the other standard exception classes in the standard library if they adequately represent your needs. [cppreference](http://en.cppreference.com/w/cpp/error/exception)

>- logic_error
>    - invalid_argument
>    - domain_error
>    - length_error
>    - out_of_range
>    - future_error(C++11)
>    - bad_optional_access(C++17)
>- runtime_error
>    - range_error
>    - overflow_error
>    - underflow_error
>    - regex_error(C++11)
>    - tx_exception(TM TS)
>    - system_error(C++11)
>        - ios_base::failure(C++11)
>        - filesystem::filesystem_error(C++17)
>- bad_typeid
>- bad_cast
>    - bad_any_cast(C++17)
>- bad_weak_ptr(C++11)
>- bad_function_call(C++11)
>- bad_alloc
>    - bad_array_new_length(C++11)
>- bad_exception
>- ios_base::failure(until C++11)
>- bad_variant_access(C++17)

`std::runtime_error` (included as part of the stdexcept header) is a popular choice, because it has a generic name, and its constructor takes a customizable message:

```c++
#include <iostream>
#include <stdexcept>
 
int main()
{
	try
	{
		throw std::runtime_error("Bad things happened");
	}
	// This handler will catch std::exception and all the derived exceptions too
	catch (std::exception &exception)
	{
		std::cerr << "Standard exception: " << exception.what() << '\n';
	}
 
	return 0;
}
```

This prints:

```
Standard exception: Bad things happened
```

## Deriving your own classes from std::exception

You can derive your own classes from `std::exception`, and override the virtual `what()` member function. Here’s the same program as above, with `ArrayException` derived from `std::exception`:

```c++
#include<iostream>
#include<string>
#include<exception>

class ArrayException :public std::exception
{
protected:
    std::string _error;
    
public:
    ArrayException (std::string error): _error(error) {}
    // return the std::string as a const C-style string
    const char* what() { return _error.c_str(); }
};

class IntArray
{
protected:
    int _data[3];
public:
    IntArray(){}
    int getLenght() { return 3; }
    int& operator[](const int index)
    {
        if (index < 0 || index >= getLenght())
            throw ArrayException("invalid index");
        
        return _data[index];
    }
};

int main()
{
    IntArray a1; 
    try
    {
        int value = a1[5];
    }
    // derived catch block go first !!
    catch (ArrayException & exception) 
    {
        std::cerr << "An array exception occured (" 
                  << exception.what() << ")\n";
    }
    catch (std::exception & exception) 
    {
        std::cerr << "Some other std::exception occured (" 
                  << exception.what() << ")\n";
    }
    return 0;
}
```

It’s up to you whether you want create your own standalone exception classes, use the standard exception classes, or derive your own exception classes from std::exception. All are valid approaches depending on your aims.

## Rethrowing an exception
http://www.learncpp.com/cpp-tutorial/14-6-rethrowing-exceptions/