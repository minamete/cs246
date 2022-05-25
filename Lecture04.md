# CS 246, Lecture 04
###### 2022 May 12

## More bash:
- `if [ expression ]; then` for if statements
- `! expression` indicates negation
- `-n stringvar` indicates if a string is non empty
- `str1=str2` for equality (identical strings), `str!=str2` for non equality 
- `echo $(( 4 + 5 ))` for arithmetic. In general, use `(( ))` to make the shell treat something like arithmetic.
- `(( a++ ))` to increment
- Use `$` to inform the shell that it's a variable
- `$0` returns a script name not a fn name
- `$awk '{print $0}'` prints the first column, `$awk '{print $1}'` the second, etc
- Conditionals: (not arithmetic)
    - `-eq` equal to
    - `-ne` not equal to
    - `-gt` greater than
    - `-ge` greater than/equal to
    - `-lt` less than
    - `-le` less than/equal to
    - Comparing strings: `if [[ "aaa" < "bbb" ]]`
    - `if [ -w file/directory ]` (if write perms), `if [ -r file/directory ]` (if read perms), `if [ -e file/directory ]` (if exists), `if [ -d file/directory ]` (if directory), `if [ -x file/directory ]` (if executable perms)
        - If the file doesn't exist, the permissions will be false
        - These check permissions for the user executing the code

## Loops
- `while [ cond ]; do ... done` for while loops
    - Example: `while [ $x -lt 5 ]; do echo $x; x=$(( x + 1 )); done`
    - Equivalent: `until [ $x -eq 5 ]; do echo $x; x=$(( x + 1 )); done`
        - `until` statements run *until* the condition is true. `while` statements run *as long as* the condition is true. The difference: if the condition is true, the `while` statement will run, but `until` will not.
- For loops: 
    - `for x in 1 2 3; do echo $x; done`
    - `for x in {0 .. 10}; do ... done` (for x from 0 to 10) 
    - `for x in {0 .. 10 .. 2}; do ... done` (for x from 0 to 10, x incrementing by 2)
    - `for (( x=1; x<=5; x++ )); do ... done`
    - Example: `for x in *.cpp; do echo $x; done`

## Functions
- Example function: `report () { if [ $1 -eq 31 ]; then echo "this month: the 31st" else echo "This is the ${1}th" fi}`
- Passing arguments: `report $( args )`
- Taking in params: use `$1`, `$2`, etc
- Number of params: `$#`

## Testing
It would've been great if I had good enough glasses to see the board properly, but I didn't. Here's a [useful link instead.](https://notes.sibeliusp.com/mdf/1185/cs246/#software-testing) I believe he talked about everything written here.
