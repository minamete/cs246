# CS 246, Lecture 01
###### 2022 May 03

Prof name: Mahmoud Bahnasy, PhD in Comp Eng

Lecture timing & location: TTh 2:30-3:50pm MC 4061

## Topics: 
- Linux
- C++ (Make sure to be OK with C pointers and structs)
- OOP, main theme of course
- C++ (including smart pointers, which help with memory management)

Assignment 0 - no grades, only to get comfortable with the environment (how to connect to the Linux machine, git clone/pull/push etc), due May 15. All assignments are available on Piazza.

In general: type in ```ssh userid@linux.student.cs.uwaterloo.ca``` to the terminal to SSH to CS servers. You can do ```ssh -Y userid@linux.student.cs.uwaterloo.ca``` when working with GUI applications to enable X11 forwarding.

[Useful resource for shell commands](https://explainshell.com)

Windows 10: start powershell terminal, type ```ssh userid@linux.student.cs.uwaterloo.ca```. You should see a different user from your windows machine username. Windows users can also use PuTTY or MobaXterm. The setting for enabling S11 in PuTTy can be found under Connection->SSH->S11.

Skipping 2FA: the instructions for this can be found in LEARN. Make sure not to copy the private key!

## Linux commands:
- `ls` lists the files and directories in the current directory.
    - `ls *.txt` lists all files ending with .txt, and `ls ?b?.txt` lists all files of the format \[char\]b\[char\] (the question mark replaces one letter). 
- `scp` copies files to/from your local machine and the remote server.
- `man` lists what commands do, stands for manual.
- `cat` is a concatenation command: add one thing to another thing. `cat [file]` means "add this file to the terminal", which dumps the contents of the file into the terminal. `cat > [file]` is output direction - the output of `cat` goes into `[file]`. Similarly, we have input direction with `cat < [file] > [file2]`.
- `diff [file1] [file2]` shows the difference between `file1` and `file2`.
- `head / tail` reads the # of lines from the top/bottom of a file.
- `wc [file]` gives the word count in a format of `[lines] [words] [characters] [filename]`. 
    - `wc -l [file]` gives *just* the line counts. Similarly, you can also count just the words and just the characters.
- `tldr` too long, didn't read - shorter. Not available on the student servers.
- `vim file.txt` to either create a new `file.txt`, or open an existing `file.txt` in vi.

This course will involve working with either vi or nano.

## vi commands:
- `i` to insert text and edit text
- `esc` after inserting and `q` to quit
- `wq` to save and quit, `q!` to quit without saving

If you want to use VS Code, it is highly recommended to skip 2FA.
