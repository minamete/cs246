# CS 246, Lecture 17
###### 2022 June 28, after an entire month I have decided to go to Hackman's section

## Recall
Compiler does virtual dispatch: calls appropriate methods based on runtime type of the object.
The keyword `override` is not necessary; it only tells the compiler that it's your intention (and so the compiler will check if there's a matching method in the base class and output errors as needed).
```
class Book {
    ...
    public:
    virtual bool isHeavy() {
        return numPages > 200;
    }
}

class Comic: public Book {
    ...
    public:
    bool isHeavy() override {
        return numPages > 50;
    }
}

Book * myBook [20]p; // not sure if the pointer is supposed to be there
myBook[0] = new Comic {...};
myBook[1] = new Comic {...};
...
for(int i = 0; i < 20; i++) {
    cout << myBooks[i]->isHeavy() << endl;
}
```

This code executes `Book::isHeavy` for Books, `Text::isHeavy` for texts, and `comic::isHeavy` for comics.
The code accomodates multiple types under one abstraction (the Book abstraction), which is **polymorphism** ("many forms").

Note: this is why our input operator takes an `istream &` but can operate on `ifstream`, `istringstream`s, etc 

## Revisiting Destructors
```
class X {
    int * x;
    public: 
    x (int n) : x {new int {n}} {}
    ~x() {delete x;}
}
class Y: public X {
    int * y;
    public: 
    Y(int m, int n) : X{n}, Y{new int[m]} {}
    ~Y() {delete y;}
}
```
There are 4 phases to object construction. There are also 4 for destruction.
```
X * myX = new Y {10,20};
...
delete myX; // PROBLEM!!
```
We're using a base class pointer and the destructor is not virtual. So the compiler runs the base class destructor instead. We need to make the destructor virtual. 

When the destructor is not virtual, this code only calls X::~X(); if it is virtual, then Y::~X() is called as part of destroying that Y. Its superclass component gets destroyed and X's destructor runs as well. 

If you have inheritance, your destructor should *always* be virtual. Even if the base class doesn't have resources to manage, the derived classes later might. If you *don't* have inheritance, don't make a virtual destructor (it is wasteful because virtual functions use an additional pointer).