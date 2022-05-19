# CS 246, Lecture 6
###### 2022 May 19

## More C++
`cout` flushes the output buffer, `cerr` flushes the error buffer. You can write to stderror using `cerr`.
C++ has casting just like C.

Example:

``` 
#include <fstream>
#include <iostream>

int main () {
    std::ifstream infile{ "input.txt" }; // Good style to use curly brackets for initialization
    std::ofstream outfile{ "output.txt" };
    int i;
    while( true ) {
        infile >> i;
        if( infile.fail() ) break; // If, say, this file DNE or doesn't have read permissions or EOF(?)
        outfile << i << std::endl;
    }
}

```

Displaying numbers:
```
#include <iostream>
#include <iomanip>

int main () {
    int i = 95; 
    cout << hex << i << endl;
    cout << i << endl; // will stay in hex form
    cout << dec << i << endl;
    float price = 2.00;
    cout << fixed << showpoint << setprecision(2) << price << endl;
}
```

More on displaying numbers:
```
#include <iostream>
#include <iomanip>
using namespace std; 

int main () {
    for(int i = 1; i <= 10; ++i ) {
        cout << setw(3) << i << endl; // setw puts spaces
    }
}
```

Creating strings:

`char * str = "hello"`

Each character is one byte (short unsigned int). Memory model is similar to C.

```
#include <string> 

std::string str = "hello";
str[1] = 'k'; //access each character like so, although str.at(1) is safer
```

Equality operators: `==`, `!=`, `>=`, `<=` can also be used on strings to compare them.

Other string methods: `str.length()`, `str.size()` (these are identical)

Example: 
```
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s;
    cin >> s; // Similarly, we have std::getline(cin, s, {optional delimiter like ','})
    cout << s << endl;
}
```

`sizeof(short)`, `sizeof(int)`, etc work the same way as C. These sizes depend on system architecture (i.e. sizes will be different on, say, 32-bit computers and 64-bit computers).

Example of delimiters and streams:
```
#include <iostream>
#include <sstream>
#include <string>
using namespace std;

int main() {
    string s;
    string s1{ "The quick brown fox\njumped over the lazy\ndog." };
    istringstream ss1{ s1 };
    while ( ss1 >> s ) {
        cout << s << endl;
    }
    string s2{ "Smith,Jane,9999" };
    istringstream ss2{ s2 };
    cout << "***" << endl;
    while( getline( ss2, s, ',' ) ) {
        cout << s << endl; 
    }
}
```

Command line args: `int main(int args, char** argv);` where `argv` is an array of char arrays (arguments) and `args` is the number of arguments. Example:
``` 
for(int arg1 = 0; arg1 < argc; argi++) {
    cout << argv[argi] << endl;
    cerr << "111" << endl;
}
```
You can pass params to main by including them when running the program: `./a.out aaaa bb cccc` for example.

## Functions
Functions in C++ use the format `returntype funcName(inputtype ....);`. When we declare a function, we don't actually 
need names for the parameters, just the types (but we will need names when we implement these functions).

#### Function forward declarations:
when two functions need to call eachother, this may be problematic for the compiler. 

Example: This will not work!
``` 
bool even(unsigned int n) {
    if (n == 0) return true;
    return odd(n-1);
}

bool odd(unsigned int n) {
    if (n == 0) return false;
    return even(n-1); 
}
```
But this can be fixed by adding `bool odd(unsigned int n);` to the top. In general, declare functions before you use them.

#### Function overloading: 
If you want to call a function that does one thing on an integer, then a function that does the same thing (but on a float instead), you no longer need to call two separate functions. You cannot do this in C, but in C++ you can do this:
```
int add1(int);
char add1(char);
```
Because you have different signatures, you don't need to rename the function.

#### Default parameter values:
If you have two functions
```
void processInput()
void processInput(bool)
```
and you don't want to repeat this code, you can use default parameter values:
```
void processInput(bool log = false)
```