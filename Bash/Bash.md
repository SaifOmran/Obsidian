#Video2 
### Intro
- A **shell in Linux** is ==a program that provides a command-line interface (CLI) for users to interact with the operating system's core, the kernel==. It acts as an interpreter, taking commands typed by the user in a human-readable language, translating them into instructions the kernel can understand, and then displaying the output to the user.
- The default shell is ==Bash==.
- To get the type of your shell
```Bash
echo $SHELL
```

- **Bash scripting** is way to run multiple commands that are typed in an executable to run them all by running the script instead of typing each command by yourself.
- The name of the script commonly ended with *.sh* like *myScript.sh*.
- The script file has to be executable as we said to run it, so we use `chmod +x [script]` to add the execute permission.
- Every script starts with ==shebang==, shebang (`#!`) is ==a special character sequence at the very beginning of a script file on Unix-like operating systems (Linux, macOS) that tells the system which interpreter to use for executing the code==.
```Bash
#!/bin/bash
#your script
```

- To run the script you have 2 methods
```Bash
#./[script_name]
#bash [script_name]
./first_script.sh
bash first_script
```
---
#Video3 
### Variables
```Bash
#var=value # no spaces
myname="saif"
# To make a variable immutable we use readonly, if there is a change on the variable it would give an error
readonly myname
myname="Ahmed" # Error

# To print the value of variable we use echo with $
echo $myname #o/p: saif

#To save an output of a command
files=$(ls)
echo $files #it will show the files
```

### Environment variables
- Those are variables which are created by the system for the current shell
```Bash
# To show all variables
env
#Examples
echo "$HOME" # -> show the home dir of the logged user
echo "$USER" # -> show the user logged in
echo "$PATH" # -> show the path where the system search for the scripts of the commands
echo "$PWD" # -> show the current working directory
echo "$BASH" # -> show the current shell
```
---
#Video4 
### Basic math operations
```Bash
# To make any math operation we use expr
expr 10 + 20
expr 10 - 20
expr 10 / 20
expr 10 % 20
expr 10 \* 20 # Here we add scape sequence as the * is WILDCARD refers to all files in a dir
```