# CS 246, Lecture 03
###### 2022 May 10

## Review:
`$ command directive(s) parameters`
Ex. `ls -a`, `cat file std out`

Three redirection places: `std out`, `std in`, `std err`

Correction: When inside square brackets, `[^chars]` means to not include chars.

You can egrep or grep through directories.

## Bash, ctd:

`bash script.sh` executes the program and prints output.

`source script.sh` executes every single line in a new terminal, as if you copied every line from the script file to the terminal.

Example: 

`var=1` 

`echo $var`

(In terminal, after) `echo $var`

Bash: nothing

Source: echoes var

Using variables example: 
`f=file`
`echo $var`
`echo ${var}`

Other variables can be found in Linux Commands.

$! is a process ID for the last executed command. 
$# is how many parameters.
&& xecutes the next command on the condition that the previous command finished successfully. For example: `g++ file -o -file && cmd`

`if ... then ... fi` is an if statement; the `fi` terminates the statement. For example: `if [ $# -ne 1 ] ... elif [ ... ] ... else ... fi`

`for x in ... do ... done`: for loop syntax.