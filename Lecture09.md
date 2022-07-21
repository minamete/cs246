# CS 246, Lecture 8
###### 2022 May 31

Why would you do 
```
g++ -c main.cc
g++ -c vec.cc
g++ main.o vec.o -o vecs
```
instead of 
```
g++ main.cc vec.cc -o vecs
```
Answer: reduce compilation time, especially in large projects with 100+ files to compile. On the bottom, you would need to add every single file and compile each and every one. On the top, you have the option of only using the linker, which takes less time.

## Makefile (ctd): 

Makefile only compiles files into `.o` files if the timestamp of the `.o` file being modified is earlier than the timestamp of the `.cc` file.

Fixed example:
```
all: vecs # only restriction on ordering of builds is that all should go on top.

vecs: main.o vec.o
    g++ main.o vec.o -o vecs # the indent has to be a tab, not spaces

main.o: main.cc vec.h
    g++ main.cc

vec.o: vec.cc vec.h
    g++ vec.cc
```

## Software design
Difference between cohesion and coupling: cohesion is the functional strength of a module, while coupling is how interdependent modules are. We want to minimize coupling while maximizing cohesion.

Procedural programming: a programming model from structural programming, where procedures (routines/subroutines/functions) are carried out.

Only difference between struct and class: classes have functions
```
struct Student {
    int assns, mt, final;
    float grade();
};
```
```
float Student::grade() {
    return assns * 0.4 + mt * 0.2 + final * 0.4;
}
```

Classes: have attributes (variables), some other thing about methods, templates

`this` keyword: pointer to the object itself.

Defining a class:

Default constructor
Create own constructor
The same rules that apply to functions also apply to constructors
Once a constructor is made, the compiler stops helping you.

``` 
Student (); 
Student (int assns);
Student (int assns, int mt);
Student (int assns, int mt, int final);
```
More efficient:
```
Student (int assns=0, int mt=0, int final=0);
```

Constructors do not return anything. Never store an arrow in the constructor, because the compiler might make a mistake.

```
S = {...} // calls constructor twice
student S{...};
S(....);
```

Constructors can call outside functions.

Something about initializing constants?

```
Student::Student(int id, int assns, int mt, int final) 
    : id{id}, assns{capGrade{assns}}, mt{capGrade{mt}}, final{capGrade{final}} {}

float Student::grade() {
    return assns * 0.4 + mt * 0.2 + final * 0.4;
}
```

This is used only for the constructor
Reduce # of cycles
member initialization

Copy constructor
```
Student S1{...} // default constructor
Student S2{S1} // copy constructor; creating an object of the same class, copies everything field by field
```