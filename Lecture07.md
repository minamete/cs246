# CS 246, Lecture 7
###### 2022 May 24

## C++ Pointers and References
We have used structs before in C. It's similar in C++:
```
struct Node {
    int x1;
   // Node n; ISSUE: Node here is not defined yet! Instead:
   Node *n; // This is a pointer
};

Node name{5,6};
```
In C, we have a `NULL` pointer. in C++, we use `nullptr` instead. Although C would work, `NULL` is equivalent to `\0` while 
`nullptr` is actually a pointer. 

You can also have default parameters in structs.

Note on file streams: if a file is opened to read but the file doesn't exist, there will be an error. But if the file is 
opened to write and doesn't exist, it'll create a new file.
``` 
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream infile("input111.txt");
    cout << "1: " << infile.fail() << endl;
    ofstream outfile("output.txt");
    cout << "2: " << infile.fail() << endl;
    istream &ifile = infile;

    int i;
    while(true) {
        infile >> i;
        if(infile.fail()) break;
        outfile << i << endl;
    }
}
```
Constants must be initialized (good convention: capitalize constants). For example: `const PI = 3.14;`

`Node n1 = {5, nullptr};` and then `const Node n2 = n1;`: deep copying, copying by value and not by reference.

Scope exists in C++ just like C.

Copying occurs in one of three ways: by value, by pointer, and by reference.

Copying by value: copy each value of a data structure into another data structure.

Copying by pointer and copying by reference: allows for editing of value. 

References are like constant pointers: pointers whose memory location pointing doesn't get changed. They have to be initialized:
e.g. `datatype & alias{5}`

`const int * ptr`: the pointer location is constant; `int const * ptr`: the integer in the location the pointer is pointing to is constant.

`program [file1 [file2]]`: optionally provide these files. 

```
switch (cond) {
    case 3: 
    ...
    break; //if the break is not included, it would keep going
    case 2: 
    ....
    break;
    default: 
    ...
    break;
}
```