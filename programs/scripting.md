# Shell scripts
- [Shell scripts](#shell-scripts)
  - [1. Example](#1-example)
  - [2. Aliases](#2-aliases)
  - [3. Variables](#3-variables)
## 1. Example
To run a shell script file .sh file: 
`bash <fileName>`

The file has to be made executable through file permission!

- Example file content:
```Bash
#!/bin/sh
#
#Comment: the first line indicate the following has to be run in the terminal
#
mkdir -p /app/assets/{images,styles,js}
touch project/app/assets/index.html project/app/assets/styles/main.css project/app/assets/js/main.js
git init
```

Useful applications: automate the cloning of an existing Github repo and setting up a standard folder structure for a new project with a copy of a template html

[Reference guide](https://www.codementor.io/celestine_eo/getting-started-with-bash-scripting-for-web-developers-rlufh8cdx)


## 2. Aliases
All the aliases stored are stored in the bash profile for the command line:
`nano ~/.bash_profile`		

Show all existing aliases in the command line:
`alias`


## 3. Variables
- Create a new variable
```
<VARNAME>=value		
MYNAME=Joris

echo $MYNAME	//-> Joris
```
A long filename or path can be stored as variable:
```
FILEPATH=/test/hello/world/1.txt
cat $FILE		//-> content of file
```