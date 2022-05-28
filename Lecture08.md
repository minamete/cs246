# CS 246, Lecture 7
###### 2022 May 26
###### I was absent so I stole these notes from Eric

## Preprocessor
`#include` includes the content of another file. For example,
```
#include <iostream>
#include "file.h"
```
`#define` defines a symbol and gives it a value.
```
#define MAX 10 // Setting value of MAX = 10
int x[MAX]; // Like int x[10]
#define FLAG // Sets this symbol to an empty string 
```
`#if` and `#endif`: helps define directives conditionally.
```
#define SECLEVEL 1 // Assignment by just a space
#if SECLEVEL == 1 
short int // suppressed if SECLEVEL != 1
#elif SECLEVEL == 2
long long int // suppressed if SECLEVEL != 2
#endif 
publickey;

#if 0
... // This will never be run because 0 = false
#endif

#ifdef NAME // true if NAME is defined
#ifndef NAME // true if NAME is undefined

#define DEBUG
int main() {
    #ifdef DEBUG
    cout << "starting program" << endl;
    #endif
}
```
Example:
```
int main() {
    cout<<X<<endl;
}
```
Then
```
gcc -DX=15 define.cc -o define
```
Where the `-DX=15` sets X to 15.

More examples:
```
int main() {
    #ifdef DEBUG
        cout<<"setting x=1"<<endl;
    #endif
    int x = 1;
    while (x < 10) {
        ++x;
        #ifdef DEBUG
            cout<<"x is now"<<x<<endl;
        #endif
    }
}

g++ -DDEBUG loop.cc
```
## Dependencies
These things can get compiled separately:
- Interfaces (.h): type definitions and prototypes (headers) of functions
- Implementation (.cc): full definition for every provided function
- Declarations: assert that the function exists
- Definition: full details of the function, with allocated space for variables and functions

For example:
```
//vec.h
struc Vec {
    int x,y;
}
Vec operator+(const Vec &v1, const Vec &v2);
```
```
//vec.cc
#include "vec.h"
Vec operator+(const Vec &v1, const Vec &v2) {
    return {v1.x+v2.x, v1.y+v2.y};
}
```
```
//main.cc (client)
#include "vec.h"
    int main() {
    vec v{1,2};
    v = v + v;
}
```
Compiling:

`g++ -std=c++14 -c vec.cc`: compile only, don't link, do not build executable for the full program. This builds the object
`vec.o`.

`g++ -std=c++14 -c main.cc`: this builds the object `main.o`.

`g++ vec.o main.o -o vecs`: links objects that we generated into the executable.

What happens when we want to change something?

If we change `vec.cc` we would need to rerun some things. If we change `vec.h` then we would need to recompile everything.

## Make
A makefile is a text file that is used/referenced by the `make` command to build. It keeps track of dependencies. [Reference 1](https://stackoverflow.com/questions/2481269/how-to-make-a-simple-c-makefile), [Reference 2](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html)

Here's an example makefile:
```
vecs: main.o vecs.o // vecs is the target to be outputted, and the entire line specifies the dependencies.
    g++ main.o vec.o -o vecs // the recipe to be executed

main.o: main.cc vec.h 
    g++ -std=c++14 -c main.cc

vec.o vec.cc vec.h
    g++ -std=c++14 -c vec.cc
```
After creating this makefile, you would run `$make` on the cmd. This would automatically run (if your code was previously uncompiled)
```
g++ -std=c++14 … main.cc
g++ -std=c++14 … vec.cc
g++ main.o vec.o -o vecs
```
If your code was previously compiled, (i.e. main.o, vec.o and vecs are still there) and you changed main.cc, then `$make` would 
only run
```
g++ -std=c++14 main.cc
g++ main.o vec.o -o vecs
```
And if nothing has changed, then `$make` would display `vecs is up to date`. 

Phony targets: in the above code, `vecs`, `main.o` and `vec.o` were targets. However, you can have targets that do not represent 
any files. These targets are called phony targets.
```
clean: 
    rm *.o vecs
```
Some other phony targets: `all`, `clean`, `install`.

You can also have variables in makefiles. They reduce clutter and generalize some of the repeated terms.
## Variables:
`CXX=g++`: name of the compiler
`CXXFlags=-std=c++14 -Wall -MMD`: compiler options, tells the compiler to include all kinds of wordings
`OBJECTS=main.o vec.o`: output objects
`DEPENDS=${OBJECTS:.o=d}`
`EXEC=vecs`

## Dependencies:
```
{EXEC}: ${OBJECTS} -o ${EXEC}
-include ${DEPENDS}
main.o: main.cc vec.h # now can omit recipe
#${CXX} ${CXXFLAGS} -c file.cc
vec.o: vec.cc vec.h
PHONY: clean
clean: rn ${OBJECTS} ${EXEC} ${DEPENDS}
g++ -MMD -c vec.cc // generates vec.d
$cat vec.d // will produce vec.o: vec.cc vec.h
```
