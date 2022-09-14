# List, View / Edit, Find
Table of contents
- [List, View / Edit, Find](#list-view--edit-find)
  - [1. List](#1-list)
  - [2. Find files and folders](#2-find-files-and-folders)
    - [Find and execute commands on result](#find-and-execute-commands-on-result)
    - [Refine search results with Grep](#refine-search-results-with-grep)

## 1. List
* List files and folders
```bash 
ls
ls -l					# -> list files with properties
ls -a					# -> show all files, incl. hidden
ls <otherFolder>		# -> shows the content of direct sub folders

ls -r					    # -> reverse alphabetical order
ls -l *.doc				# -> list all doc files

less          # when the output is too long to read comfortably
ls | less 

cat <filename>        # output content in the terminal
cat <file1> <file2>		# concatenate in terminal the content of both files

diff <file> <file>	    # compare content of two files

tail -f <file>		    # shows the end of a file's content and additional lines in real-time, ideal for following log files.
```
## 2. Find files and folders
```bash
# '.' to look inside all sub-directories of current folder
# max-depth=1 to exclude sub-folders
# find name that ends with "*.txt" or starts with "name*"
#-iname removes case sensitivity
find . -type f -name "*.txt"  

find max-depth=1 -type d -iname "www*"   

# Examples:
find /etc -type f -iname "*.conf"  # all config files

find . -type f -perm 0664          # all files with permission level

find . -type f -size +100k         # all files with sizes > 100k

find . -type f -not -iname "*.php"  # exclude certain file types
```
### Find and execute commands on result
```bash
# find all .php files and delete them
find . -type f -iname "*.php" -delete

# For most other linux commands you need to use the -exec syntax
# End the command with \;
# find all .php files move them to tmp folder or copy all folders (incl. sub-folders)
find . -type f -iname "*.php" -exec mv '{}' ~/tmp \;
find . -type d -iname "www" -exec cp -r '{}' ~/tmp \;
```
### Refine search results with Grep
Search for or filter strings of text inside files or inside program output.
```bash
# grep "str" <filename> <filename> <filename>
grep "function" main.js secondary.js third.js

# arguments: -i -n ./* to remove case sensitivity, to display the line number the string was found and to look into all files
grep -n -i "function" ./*

grep "string"	/home -r	             # include files in sub folders
grep "string1\|string2" /etc -r		   # search for multiple strings 

# grep is often combined with other commands to filter output
cat system.log | grep "error"        # find all errors inside a log file
```