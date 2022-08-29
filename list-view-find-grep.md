# List, View / Edit, Find
Table of contents
- [List, View / Edit, Find](#list-view--edit-find)
  - [1. List](#1-list)
  - [2. View / Edit files](#2-view--edit-files)
  - [3. Find / locate files](#3-find--locate-files)
    - [Search files](#search-files)
    - [Find and execute commands on result](#find-and-execute-commands-on-result)
  - [4. Grep](#4-grep)

## 1. List
* List files and folders
```
ls
ls -l					//-> list files with properties
ls -a					//-> show all files, incl. hidden
ls <otherFolder>		//-> shows the content of direct subfolders

ls -r					//-> reverse alphabetical order
ls -l *.doc				//-> list all doc files
```
* Get disk usage
```
du <folder>				//-> disk usage in mb of a folder and subfolders
du -sh					//-> summarize (without subfolders), human readable (in kb/mb)
du ~ -sh				//-> disk usage of home folder
```

## 2. View / Edit files 
```
nano	<file>		//-> open in file editor

cat <file>			//-> read content of file in terminal

cat file1 file2		//-> concatenate both file contents

less <fileName>		
//-> for long text files, enables pageUp/Down, use space bar, search for text etc.

dif <file> <file>	//-> compare content of two files

tail -f <file>		//-> shows the end of a files content and will show additional lines in real-time, ideal for following log files.
```

## 3. Find / locate files
### Search files
`find . -type f -name "*.txt"`
`find . -type f -iname "file*"`
'.' to look inside all subdirectories, '-type f/d' to look for files/dir, '-name' "ends in .txt",
'-iname' to remove case sensitivity

Examples:
- finds all configuration files in /etc

`find /etc -type f -iname "*conf"`

- find all files with permission level of 0664
`find . -type f -perm 0664`

- all files above 100kb file size OR below -100k
`find . -type f -size +100k`

- all files that are NOT of a specific type
`find . -type f -not -iname "*.php"`

### Find and execute commands on result
- find all .php files and DELETE them:
`find . -type f -not -iname "*.php" -delete `

1. look for file types
2. on the returned result execute a GREP/other command
3. end -exec with {} +
`find . -type f -iname "*.js" -exec grep -i -n "string" {} +` 

- find all .php files move them to tmp folder
`find . -type f -not -iname "*.php" -exec mv {} ~/tmp \`

## 4. Grep
Search for or filter strings of text inside files.
```
grep "str" <filename> <filename> <filename>
grep "function" main.js secondary.js third.js
```
Arguments: -i -n ./* to remove case sensitivity, to display the line number the string was found and to look into all files
```
grep -n -i "function" ./*

grep "string1\|string2"			//-> search for multiple strings
grep "string" /etc -r			//-> include files in subfolders 

cat system.log | grep error		//-> find all errors inside a log file
```