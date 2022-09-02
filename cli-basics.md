# Basic Linux CLI Commands
A *console* is a physical device. A *terminal* is a text input and output environment, a program. Git Bash is a terminal window. The terminal is the program in which the shell will run. A *shell* is a program that acts as command-line interpreter. It processes commands and outputs the results. The default shell in Linux is *Bash*. In Windows it is PowerShell. Shells work also as programming languages, in the sense that with them we can build scripts to make our computer execute a certain task.

* Command history
```bash
echo $0     # show type of Shell
history -c						# clear cli history 
CTRL + R						# search command history
history | grep <command>		# show all related commands 
history | less					# allows you to browse history
```
* Inline navigation
```bash
CTRL + A		# Go to beginning of the line
CTRL + E		# Go to end of the line

ALT+F			# jump from word to word
ALT+B			# jump from word to word backwards
```
* Folder navigation
```bash
cd ..			# move up one level (cd: change directory)
cd				# HOME
cd ~			# HOME
 
cd /			# root

cd /usr/share	# absolute path
cd app/assets	# relative path, in the same folder

# cd <+> TAB autocompletes the dir name present in the current folder

pushd /dir		# jump to folder on the other end of filesystem
popd			# go back to previously stored folder
```
* Escape characters

Use the \ to escape characters in folders or files with spaces in them. Thus: `cd dir\ with\ spaces` brings you to 'dir with spaces'.
* Execute binaries

Commands are looked up in the /bin directory, but not in the current directory. If that is where you're executable is located you have to add a dot and slash: `./<file.exe>`
* Help and manual pages
```bash
whatis <command>

apropos <something>		# list of related commands

man <program>			# manual for the program 

<program> --help

info <command>
```