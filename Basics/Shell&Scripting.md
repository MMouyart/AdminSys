# Using the shell and automate tasks using scripts
## Shell
Basic stream operators
```bash
>       Redirect STDOUT to a file; Overwrite if exists
>>      Redirect STDOUT to a file; Append if exists
2>      Redirect STDERR to a file; Overwrite if exists
2>>     Redirect STDERR to a file; Append if exists
&>      Redirect both STDOUT and STDERR; Overwrite if exists
&>>     Redirect both STDOUT and STDERR; Append if exists
<       Redirect STDIN from a file
<>      Redirect STDIN from the file and send the STDOUT to it
```

Run a command independently from terminal session
```nohup <cmd>```

Wildcards to use with commands
```bash
* means any string
? means any single character
[ABC] matches A, B, or C
[a-k] matches a, b, c, ..., k (both lower-case and upper-case)
[0-9a-z] matches all digits and numbers
[!x] means NOT X
```

Setting environment variable and display it (convention is name in CAPS)
```bash
MYVAR=value
echo $MYVAR
```

Unsettting environment variable (considering it exists)
```unset MYVAR```

Exporting environment variables (to still exist after reboot)
```bash
export MYVAR
export MYVAR    =value
```

Saving a command output in a variable
```bash
VAR=$(cmd)
ex : VAR=$(ls -ah)
VAR=`cmd`
ex : VAR=`ls -ah`
```

Using a variable in a command
```bash
cmd arg1`VAR`
ex : touch backup_`date`.tar.gz
cmd arg1$VAR
ex : touch file$VAR
```

Refresh bash configuration file
```bash
source ~/.bashrc
source ~/.zshrc
```

Defining aliases
```bashalias myalias='ls'
source ~/.bashrc
```

Defining function
```bash
myfunc (){
echo "this is my function the parameters are $@"}
source ~/.bashrc
```

Running a custom script
```bash
Inside a new sub-process        ./script
In the same process             source script
```

Running a script with scource keeps declared variables in the main terminal process

Combining commands
Combine commands normally (no logical execution)
```cmd1 ; cmd2```

Combine command only if previous command succesful (logical AND execution)
```cmd1 && cmd2```

Combine command only if previous command not succesfull (logical OR execution)
```cmd1 || cmd2```

## Grep
Grep common options
```bash
-c      just show the count
-v      reverse the search
-n      show line numbers
-l      show only file names
-i      case insensitive
-r      Read all files under each directory, recursively
```


## Vi
Vi moving cursor
```bash
h       One character to the left (only current line)
j       One line down
k       One line up
l       One character to the right (only current line)
w       Next word on the current line
e       Next end of word on the current line
b       Previous beginning of the word on the current line
Ctrl-f  Scroll forward one page
Ctrl-b  Scroll backward one page
```

Jumping around
```bash
G       With no number, will jump to the end & 10G will jump to line 10
H       5H will go to the 5th line from the top of the screen
L       3L will move the cursor to the 3rd line to the last line of the screen
```

Editing text
```bash
i       Enter the insert mode
a       Enter the insert mode after the current position of the cursor
r       replace only one character
o       open a new line below the cursor and go to the insert mode
O       open a new line above the cursor and go to the insert mode
c       clear to a location and go to the insert mode the replace till there and then normal insert (cw will overwrite the current word)
d       delete. you can mix with w (dw) to delete a word. Same as cw but dw does not to to the insert mode
dd      Delete the current line
x       Delete character at the position of the cursor
p       Paste the last deleted text after the cursor
P       Paste the last deleted text before the cursor
xp      swaps the character at the cursor position with the one on its right
```

Searching
```bash
/       Search forward (/happiness will find the next happiness)
?       Search backward
n       repeat previous search. You can also use / and ? without any parameters)
```

Exiting
```bash
:q!     Quit editing without saving = runaway after any mistake
:w!     Write the file (whether modified or not). Attempt to overwrite existing files or read-only or other unwritable files
:w myfile.txt   Write to a new name
ZZ      Exit and save the file if modified
:e!     Reload the file from disk
:!      Run a shell command
```

Using env to set, remove, display variable or run command with specific variable values
```env [option]... [NAME=VALUE]... [COMMAND [ARGS]...]```

Options
```bash
-u NAME Remove variable NAME from the environment, if it was in the environment.
--unset=NAME Remove variable NAME from the environment, if it was in the environment.
-i Start with an empty environment, ignoring the inherited environment.
--ignore-environment Start with an empty environment, ignoring the inherited environment.
```

Using set to change/display bames and values of shell variables
```bash
switch          result
-b              Cause the status of terminated background jobs to be reported immediately, rather than before printing the next primary prompt.
-e              return in case a pipline, command, ... return non-zero
-n              Read commands but do not execute them; this may be used to check a script for syntax errors. This option is ignored by interactive shells.
-t              Exit after reading and executing one command.
-C              Prevent output redirection using ‘>’, ‘>&’, and ‘<>’ from overwriting existing files.
```


## Shell scripting
Starts with shebang
```bash
#!/bin/bash
#/bin/sh
```

Running commands
```bash
echo "This is an echo"
ls -ltrh
```

Using variables
```bash
VAR=value
echo "my var equals $VAR"
```

Using parameter arguments
```bash
echo "this is the 1st argument $1"
echo "this is the 2nd argument $2"
echo "these are all arguments $@"
echo "there are $# parameters"
```

Using if condition
```bash
if [condition]
  then
    do something
  else
    do something else
  fi
```

If conditions examples
```bash
"a" = "b"       if two strings are equal (here it will return False)
"a" != "b"      string a is not equal to string b
4 -lt 40        if 4 is lower than 40 (True)
5 -gt 15        if 5 is greater than 15 (False)
5 -ge 5 if 5 is greater or equal 5
5 -le 3 if 5 is lower or equal to 3
9 -ne 2 9 is not equal with 2 (True)
-f FILENAME     if file FILENAME exists
-s FILENAME     if file exists and its size is more than 0 (Zero)
-x FILENAME     if file exists and is executable
```

Read input
```bash
read VAR
read -t 10 -p "What is your name" NAME
```

For loop
```bash
for VAR in SOME_LIST;
  do
    some stuff with $VAR
    some other stuff
done

for NUM in 1 2 3 4 5 6;
  do
    echo $NUM
done

for FILE in $(ls);
  do
    echo $FILE
    wc -l $FILE
done
```

While
```bash
while [condition]
  do
    do something
    do anohter thing
done
```
