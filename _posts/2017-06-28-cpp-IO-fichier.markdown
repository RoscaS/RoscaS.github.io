---
layout: post
title: "Cpp: I/O dans des fichiers"
subtitle:  lecture et écriture dans des fichiers
date: 2017-06-28
author: Sol
category: Cpp
tags: cpp 
finished: false
mathjax: true
---


## Base

### 3 classes

* `ifstream` $ \Rightarrow $ lecture
* `ofstream` $ \Rightarrow $ ecriture
* `fstream` $ \Rightarrow $ les deux

### Ouvrir un fichier

```cpp
open(filename, mode)
```

* `filename` $ \Rightarrow $ string qui représente le nom du fichier
* `mode` $ \Rightarrow $ pramètre optionnel qui est une combinaison des flags suivants:

<br>
<br>

*  `ios::in` $ \Rightarrow $ open for input operations 
 * `ios::out` $ \Rightarrow $ open for output operations 
 * `ios::binary` $ \Rightarrow $ open in binary mode 
 * `ios::ate` $ \Rightarrow $ set the initial position at the end of file if not set pos=beginning of the file 
 * `ios::app` $ \Rightarrow $ all output operations are performed at the end of the file, **APPENDING** the content to the current content of the file 
 * `ios::trunc` $ \Rightarrow $ override the content of the file

Tous ces flags peuvent être combinés avec l'opérteur `|` (or bitwise).

* Mode **binaire** écrit et lit sans prendre en compte le format   
* Mode **non-binires** (fichiers textes) 

```cpp
ofstream myfile;
myfile.open("example.bin", ios::out | ios::app | ios::binary);
```

équivalent à:

```cpp
ostream myfile("example.bin", ios::out | ios::app | ios::binary);
```

Chacune des méthodes `open()` des classes `ofstream`, `ifstream`, `fstream` a un mode par défaut qui est utilisé si le fichier est ouver sans argument:

* ofstream $ \Rightarrow $ `ios::out`
* ifstream $ \Rightarrow $ `ios::in`
* fstream $ \Rightarrow $ `ios::in | ios::out`

Les modes par défaut de `ifstream` et `ofstream` sont combiné avec les éventuelles paramètres.

Pour `fstream`, le mode par défaut n'est appliqué automatiquement si on ajoute des paramètres.

Pour vérifier si un fichier à bien été ouver on utilise `is_open()`

```cpp
if (myfile.is_open()) { cout << "Fichier ouvert"; }
```

### fermeture

```cpp
myfile.close();
```

### fichiers texte

Text file streams are those where the ios::binary flag is not included in their opening mode. These files are designed to store text and thus all values that are input or output from/to them can suffer some formatting transformations, which do not necessarily correspond to their literal binary value.


### ecriture

Writing operations on text files are performed in the same way we operated with cout:

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () 
{
    ofstream myfile ("example.txt");
    
    if (!myfile.is_open())
    {
        cout << "Unable to open file";
        exit(-1);
    }
    myfile << "This is a line.\n";
    myfile << "This is another line.\n";
    myfile.close();
    return 0;
}
```
fichier example.txt:

```
This is a line.
This is another line.
```

### lecture

Reading from a file can also be performed in the same way that we do with cin:

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main () {
    string line;
    ifstream myfile ("example.txt");
    if (!myfile.is_open())
    {
        cout << "Unable to open file";
        exit(-1);
    }
    
    while ( getline (myfile,line) )
    {
        cout << line << '\n';
    }
    myfile.close();
    return 0;
}
```

output:

```
This is a line.
This is another line.
```

We have created a while loop that reads the file line by line, using getline. The value returned by getline is a reference to the stream object itself, which when evaluated as a boolean expression (as in this while-loop) is true if the stream is ready for more operations, and false if either the end of the file has been reached or if some other error occurred.

## Flags d'état

Les méthodes suivantes servent à vérifier des états spécifiques d'un flux (ils retourne tous un bool)

* `bad()` $ \Rightarrow $ Returns true if a reading or writing operation fails. For example, in the case that we try to write to a file that is not open for writing or if the device where we try to write has no space left.

* `fail()` $ \Rightarrow $ Returns true in the same cases as bad(), but also in the case that a format error happens, like when an alphabetical character is extracted when we are trying to read an integer number.

* `eof()` $ \Rightarrow $ Returns true if a file open for reading has reached the end.

* `good()` $ \Rightarrow $ It is the most generic state flag: it returns false in the same cases in which calling any of the previous functions would return true. Note that good and bad are not exact opposites (good checks more state flags at once).

* `clear()` $ \Rightarrow $  can be used to reset the state flags.

## Position

All i/o streams objects keep internally -at least- one internal position:

`ifstream`, like `istream`, keeps an internal **get** position with the location of the element to be read in the next input operation.

`ofstream`, like `ostream`, keeps an internal **put** position with the location where the next element has to be written.

Finally, `fstream`, keeps both, the **get** and the **put** position, like `iostream`.

These internal stream positions point to the locations within the stream where the next reading or writing operation is performed. These positions can be observed and modified using the following member functions: 

`tellg()` and `tellp()`
These two member functions with no parameters return a value of the member type streampos, which is a type representing the current get position (in the case of `tellg`) or the put position (in the case of `tellp`).

`seekg()` and `seekp()`
These functions allow to change the location of the get and put positions. Both functions are overloaded with two different prototypes. The first form is:

```cpp
seekg ( position );
seekp ( position );
```

Using this prototype, the stream pointer is changed to the absolute position position (counting from the beginning of the file). The type for this parameter is **streampos**, which is the same type as returned by functions `tellg` and `tellp`.

The other form for these functions is:

```cpp
seekg ( offset, direction );
seekp ( offset, direction );
```

Using this prototype, the get or put position is set to an offset value relative to some specific point determined by the parameter direction. offset is of type streamoff. And direction is of type seekdir, which is an enumerated type that determines the point from where offset is counted from,and that can take any of the following values:


`ios::beg` $ \Rightarrow $ 	offset counted from the beginning of the stream
`ios::cur` $ \Rightarrow $	offset counted from the current position
`ios::end` $ \Rightarrow $	offset counted from the end of the stream

### Calculer la taille d'un fichier

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () {
    streampos begin,end;
    ifstream myfile ("example.txt");
    begin = myfile.tellg();
    myfile.seekg (0, ios::end);
    end = myfile.tellg();
    myfile.close();
    cout << "size is: " << (end-begin) << " bytes.\n";
    return 0;
}
```

output:

```
size is: 40 bytes.
```

<span style="color:red"> noter le type des variables begin et end ! </span>  
`streampos size;`

## Fichiers binaires

For binary files, **reading and writing data with the extraction and insertion operators (`<<` and `>>`) and functions like `getline` is not efficient**, since we do not need to format any data and data is likely not formatted in lines.

File streams include two member functions **specifically designed** to read and write binary data sequentially: `write` and `read`. The first one (`write`) is a member function of `ostream` (inherited by ofstream). And `read` is a member function of `istream` (inherited by ifstream). Objects of class fstream have both. Their prototypes are:

```cpp
write ( memory_block, size );
read ( memory_block, size );
```

Where **memory_block** is of type char* (pointer to char), and represents the address of an array of bytes where the read data elements are stored or from where the data elements to be written are taken. The size parameter is an integer value that specifies the number of characters to be read or written from/to the memory block.

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () {
    streampos size;
    char *memblock;

    ifstream file ("example.bin", ios::in|ios::binary|ios::ate);
    if (!file.is_open())
    {
        cout << "Unable to open file";
        exit(-1);
    }

    size = file.tellg();
    memblock = new char [size];
    file.seekg (0, ios::beg);
    file.read (memblock, size);
    file.close();

    cout << "the entire file content is in memory";

    delete [] memblock;
    return 0;
}
```

In this example, the entire file is read and stored in a memory block.

At this point we could operate with the data obtained from the file. But our program simply announces that the content of the file is in memory and then finishes.

## Buffer et sync

When we operate with file streams, these are associated to an internal buffer object of type **streambuf**. This buffer object may represent a memory block that acts as an intermediary between the stream and the physical file. For example, with an `ofstream`, each time the member function put (which writes a single character) is called, the character may be inserted in this intermediate buffer instead of being written directly to the physical file with which the stream is associated.

The operating system may also define other layers of buffering for reading and writing to files.

When the buffer is **flushed**, all the data contained in it is written to the physical medium (if it is an output stream). This process is called synchronization and takes place under any of the following circumstances: 

* When the file is closed: before closing a file, all buffers that have not yet been flushed are synchronized and all pending data is written or read to the physical medium.

* When the buffer is full: Buffers have a certain size. When the buffer is full it is automatically synchronized.

* Explicitly, with manipulators: When certain manipulators are used on streams, an explicit synchronization takes place. These manipulators are: `flush` and `endl`.

* Explicitly, with member function `sync()`: Calling the stream's member function `sync()` causes an immediate synchronization. This function returns an int value equal to -1 if the stream has no associated buffer or in case of failure. Otherwise (if the stream buffer was successfully synchronized) it returns 0.