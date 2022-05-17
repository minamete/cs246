# CS 246, Lecture 5
###### 2022 May 17

## Clarification on globbing
\* is for any number of characters.
? is for one character.
[0-9], A-Z etc are for one character. 
Examples: `*[0-9]*`, `*a*.c`, `?a`

To use regular expressions: `ls ... | egrep "^a+"` is equivalent to `a*`

## More on testing
Integration tests: When writing complex tests, we want to know how parts work with other parts, similar to blackbox tests. Testing each module to see how they work together.

Functional tests: When you have the function of the code, and a certain input, we see if it matches the expected output. Similar to blackbox tets, we use bash scripts for this (testing inputs and outputs and comparing the outputs).

Acceptance tests (e.g. alpha/beta tests): Software company gets testers to test code (alpha phase), and once they are satisfied, they will deploy the software for a certain group of users to do beta tests. Keep in mind, testing doesn't prove correctness, it proves wrongness.

Regression tests: When you've done the acceptance tests and it's been deployed in a bigger environment.

Performance tests: When you test your applications in a very stressful environment (e.g. the software is expected to work with 100 users, but you test it against 10000 users).

## C++ 
Book recommendation: C++ in a nutshell and practical C++ programming

We use C++ standard version 14. 
Set an alias for compilation: `g++14="g++ -std=c++14 -Wall -g"`

C version: 

```
#include <stdio.h>
int main() {
    printf("hello world \n");
}
```

C++ version:

```
#include <iostream>
int main() {
    std::cout << "hello world" << std::endl; // you can replace endl with a \n at the end of hello world
}
```

Compiling: 
`g++ -std=c++14 -Wall -g test0.cc -o nameoffile`, or with our alias, `g++14 test0.cc -o nameoffile`

`std` is a namespace (similar to Java packages), we can use the contents of a namespace with `::`. For example: `std::cout`.

In this course we will use `.cc` as an extension for C++ code.

C++ is cross-compatible, you can use C code. However, in the course, you will lose marks if you use C code in replacement of C++
(like `printf` instead of `cout`)

Instead of needing to specify the namespace every time we use its contents, we can use `using namespace` instead. We can rewrite
the above code as:

``` 
#include <iostream>
using namespace std;
int main() {
    cout << "hello world" << endl; 
}
```

Taking input:

``` 
#include <iostream>
using namespace std;
int main() {
    int x;
    cin >> x;
    cout << "You entered " << x << endl; 
}
```

What happens if the input isn't an integer? `cin` has a function `cin.fail()` that returns a `bool` (true/false) if the function 
gets what it expected to get (as specified by `int x`). Also, `cin` ignores whitespaces (spaces, newlines, tabs).
- More specifically, `cin` doesn't take input until the first non-whitespace character, and stops taking input at the next whitespace.
- We can also chain inputs and outputs:
    - `cin >> x >> y` to input to both x and y
    - `cout << "hello " << "world" << endl` to output "hello world"

`operator<<` is an operator that takes two parameters. ``operator<<(cout, "hello")` is equivalent to `cout << "Hello"`, and 
`operator>>(cin, x)` is equivalent to `cin >> x`. In fact, `<<` calls `operator<<(lhs, rhs)` and `>>` calls `operator>>(lhs, rhs)`.

`while` loops, `if` statements are syntactically the same as C. `cin.clear()` clears the error flag, and `cin.ignore()` ignores the rest of the input.

`cin.eof()` checks for end of file.