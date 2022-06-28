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
};
class Y: public X {
    int * y;
    public: 
    Y(int m, int n) : X{n}, Y{new int[m]} {}
    ~Y() {delete y;}
};
```
There are 4 phases to object construction. There are also 4 for destruction.
```
X * myX = new Y {10,20};
...
delete myX; // PROBLEM!!
```
We're using a base class pointer and the destructor is not virtual. So the compiler runs the base class destructor instead. We need to make the destructor virtual. 

When the destructor is not virtual, this code only calls `X::~X()`; if it is virtual, then `Y::~X()` is called as part of destroying that Y. Its superclass component gets destroyed and X's destructor runs as well. 

If you have inheritance, your destructor should *always* be virtual. Even if the base class doesn't have resources to manage, the derived classes later might. If you *don't* have inheritance, don't make a virtual destructor (it is wasteful because virtual functions use an additional pointer).
```
class Student {
    ...
    protected:
        int numCourses;
    public:
        virtual int fees() const;
        ...
};
```
There are two kinds of Students: regular and coop.
```
class Regular: public Student {
    ...
    public: 
        int fees() const override; // code to compute fees 
                                   // for regular students
};

class Coop: public Student {
    ...
    public: 
        int fees() const override; 
        // code to compute fees for coop students
}
```
BUT: what should we put for `Student::fees()`?
- Nothing? But we can't put nothing, then the function won't link!
We're not sure: every student should be regular or coop, there should be no `Student` objects.

Answer: In the student class, we write
```
class Student {
    ...
    protected:
        int numCourses;
    public:
        virtual int fees() const = 0;
        ...
};
```
This makes `Student::fees()` a **pure virtual** method, which is a method that does not require an implementation. A class with a pure virtual method is called **abstract** and cannot be instantiated. This is exactly what we want, as there should be no pure `Student` objects.
```
Student s; // error: Student is abstract
```
The purpose of abstract classes is to define common interfaces and organize subclasses. Subclasses of an abstract class are also abstract unless they implement all pure virtual methods. Non-abstract classes are called **concrete**. In UML, visualize abstract classes and pure virtual methods by italicizing their names.

## Templates
Huge topic, we're just going to learn some highlights.
```
class List {
    struct Node;
    Node *theList;
    ...
};
```
What if we want to store something other than `int`s in our `List`? A **template class** is a class parameterized by a type. 
```
template <typename T> // T can be anything you want, it's a variable name
class Stack {
    int size;
    int cap;
    T * contents; // instead of an int pointer, it will be a pointer of (unknown, arbitrary) type T
    public: 
        Stack() {...}
        void push(T X) {...}
        T top() {...}
        void pop() {...}
};
```
We rewrite `List` as a template class:
```
template <typename T>
class List {
    struct Node {
        T data;
        Node *next;
    };
    Node *theList;
    public:
        class Iterator {
            Node *p;
            ...
            public:
                ...
                T &operator*() {
                    return p->data; // doesn't matter that p->data is a T now
                }
                ...
        };
        void addToFront(T x) {
            theList = new Node{X, theList};
        }
}
```
Usage:
```
List<int> l1;
List<int> l2;
List<List<int>> l3;
l1.addToFront(3);
l3.addToFront(l1);

for(List<List<int>>::Iterator it = l3.begin(); it != l3.end; ++it) {
    for(auto n: *it) {
        cout << n << " ";
    }
    cout << endl;
}
```
How do templates work? Roughly speaking: the compiler sees each instantiation of the class (e.g. `List<int>` or `List<List<int>>`) and generates specialized classes for each type declared. You have to declare templates in the header file!

Restriction on what type(s) T can be are set by you, depending on how you use T. This is called `duck-typing`: if it walks like a duck, and it quacks like a duck, then it's a duck.

## The Standard Template Library
\*Technically called the "C++ standard template library"

The standard template library is a large collection of useful templates. For example, it includes dynamic-length arrays (vectors):
```
#include <vector>
using namespace std; 
...
// Difference between {} and ()
vector<int> v{4,5}; // vector containing (4, 5)
vector<int> w(4,5); // vector containing 4 5s (5, 5, 5, 5)
v.emplace_back(6); // v = (4, 5, 6); more efficient than push_back
v.emplace_back(7); // v = (4, 5, 6, 7)

// There is really no reason to use push_back instead of emplace_back in modern C++.
vector<Book> b;
b.push_back(Book{"A", "author", 75}); // constructs a temporary book and moves it into the back of the vector
b.emplace_back("A", "author", 75); // constructs the object directly in the back of the vector
```
Never try to use an array of objects polymorphically, because it will get sliced into the base class. You *can*, however, use an array of *pointers* polymorphically. 

`vector` doesn't know to delete parts of your array. If you have a `vector` of pointers, you have to manually `delete` them. `vector` also has built-in support for iterators. In fact, almost all STL containers have iterators!

Looping over vector:
```
for (int i = 0; i < v.size(); ++i) {
    cout << v[i] << endl; 
}
```
As we just observed, vectors also support the iterator abstraction!
```
for(vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    cout << *it << endl; 
}
```
or indeed
``` 
for(auto n: v) { cout << n << endl; }
```
To iterate in reverse (we can use `auto` in the typename still):
```
for(vector<int>::reverse_iterator it=v.rbegin(); it != rend(); ++it) {
    ...
}
v.pop_back() // removes the last element of v
```
Vectors are guaranteed to be implemented as arrays internally. Now that you know about vectors, you should use them whenever you need a dynamic-length array. i.e. you shouldn't need to use the array forms of `new` and `delete`.

Many other vector operators are based on iterators. e.g. `erase` method, which takes an iterator to be the element you want to remove.

Caution: iterators store pointers. `vector` automatically reallocates if it goes out of range. Reallocation can invalidate old iterators.