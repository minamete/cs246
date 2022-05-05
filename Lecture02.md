# CS 246, Lecture 02
###### 2022 May 05

## Linux Commands, ctd
- `wc < file.txt` input redirection, doesn't display `file.txt` at the end.
    - `wc *.txt` does `wc` for all files matching `*.txt`.
    - You can also do output redirection with `>`. This can be useful when debugging. In this case, you would use 
    `cat file.txt 2> errFile`, where the `2` tells it to read from the 2nd output (which is the error output).
- `sort` sorts everything in the file line-by-line.
- `uniq` displays unique lines only.
- Variables can be denoted with `$`. For example: `echo "$(whoami)"` and `echo "$(date)"`.
    - `var=1`
    `echo $var` prints `1`.
- `egrep` is equivalent to `grep -E`, and allows for searching via either text matches or regular expression matches.
- `la` is equivalent to `ls -A` and `ll` is equivalent to `ls -l`.

## Redirections and piping
Any Linux file *must* end with a newline. 

Standard output has buffering: sometimes, you might not see the output immediately after it's supposed to be printed. The system 
prints output, not the program. You should flush the buffer when you want to see the next line (with `endl` in C++).

Redirections for stdin/out/err goes to files. You can use `<` to redirect to stdin, `>` for stdout, `2>` for stderr.

Pipes (indicated by `|`) are like redirections, but for commands: `cat file | wc` is equivalent to `wc file`. Instead of redirections to files, pipes redirect to programs.

## Pattern matching
You can use `egrep` or `grep` to do pattern matching to search for text in files. You could also use regular expressions. 

For example: `egrep "(cs|CS)246" file.txt` searches for all instances of either cs246 or CS246 within `file.txt`.

## Regex patterns
- `^---` beginning of line
- `---$` end of line
- `(s|S)` match any of `s` or `S`
- `[cC]` match any of `c` or `C`
- `---+` match one or more
- `---*` match zero or more

## Permissions
Permissions: Read, write, execute

Permissions can be given to the owner of the file/directory (abbreviated u for user), other users in the same group as the owner (g for group), other users not in the group (o for others), and all users (a for all).

Permissions for a directory/file can be changed with `chmod`. Example: `chmod u-r xx.txt` removes the read permission for the owner for xx.txt, while `chmod g+r xx.txt` gives users in the group (not the owner) the read permission. 

`chmod` can also take numbers.

Permissions can be displayed as a string of text in the format [directory flag][owner permissions][group permissions][other permissions]. Example: `drwxrwxrwx`, where r = read permission, w = write permission, x = execute permission.

## Bash scripts
Scripts are useful for executing a set of commands from one file. The commonly used script extension is `.sh`. 

Comments should be denoted with a `#` in front. At the start of every bash script, write `#!./bin/bash` to say that 
this is a bash script (as opposed to, say, python scripts). Then, under that, write a series of commands.

Execute these scripts using `bash script.sh` or `./script.sh`, but make sure you have the permissions to execute the script.
If not, `chmod u+x script.sh` to give yourself execute permissions.