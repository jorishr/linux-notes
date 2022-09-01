# File and directory manipulation commands
Table of contents
- [File and directory manipulation commands](#file-and-directory-manipulation-commands)
  - [1. Directories](#1-directories)
  - [2. File Operations](#2-file-operations)

## 1. Directories 
* Create (nested) folders:
```
mkdir <name>
mkdir -p nesting/more/directories	
```
Create two sub folders and in each subfolder it also create three sub folders
```
mkdir -p folder/{sub1,sub2}/{sub1,sub2,sub3}
```
Example: hundred sub folders with each 10 sub folders inside
```
mkdir -p folder/{1..100}/{1..10}
```
* Remove folders
```
rm dir/*        //-> removes all files inside the folder
rm dir/*.txt    //-> removes all txt files inside the folder

rm -rf <dirname>	//-> remove folder 
rm -r <dirname> 

NOTE: -rf flag: recursive force. To avoid being prompted for every file or subfolder.
```
* Move and copy directories
```
mv <map> <destPath>
mv <map1> <map2> <destPath>

cp <map> <destPath>

cp -R * /new/absolute/path	
//-> copy CURRENT folder including all files and sub folders to a new folder. -R is recursion

cp -R test/dir1/* test/dir2		
//-> copy CONTENT: all files and sub folders inside dir1 to dir2
```

## 2. File Operations
* Create new files

Note: touch a file to update the date of last change.
```
touch <new file.extension>		//-> create new file
touch index.html	

touch app/assets/css/main.css	

touch /app/assets/js/main.js /app/assets/css/index.html
//-> creates multiple files at different locations

touch app/assets/{dir1,dir2,dir3}/text.txt
touch {project1,project2,project3}/index.html
//-> same file in multiple folders
```
* Move, rename and copy files
```
mv <file> <destPath>
mv <file1> <file2> <destination-path>   //-> move a file(s)

cp <file> <newFileName>     //-> create a copy
cp file1 file_copy          //-> copy a file into same directory 

cp <file1> /home/docs/<file_copy>       //-> copy to different dir
cp test.txt ./subfolder/test.text

cp test1.txt test2.txt test3.txt ./subfolder
//-> copy MULTIPLE files to another folder

cp * <newFolder>            //-> copy all files current dir to new dir
cp *.txt <newFolder>        //-> copy all .txt files to new dir

mv <file> <newName>         //-> RENAME a file
```
* Remove files
```
rm <filename>	
rm <filename> <filename> <filename>     //-> remove multiple files

rm *.txt        //-> remove all txt files in the current folder
rm file*        //-> remove all files that start with ...
```
Operations in the current folder:
```
rm *            //-> remove all FILES in the current folder
rm * -r         //-> remove all files AND FOLDERS	
```
Operations on a subfolder
```
rm subfolder/*      //-> remove all files in a subfolder
```
* Get File Type Info
```
file .file          //-> file type info
``` 
* Search or locate files in the file system
```
locate <file>
locate <part-of-file>
```
Note that locate needs a database that may need to be updated. Usually linux does this once a day by itself, depending on the distro. To update manually: `sudo updatedb`

Find and delete files with specific name. In the example below we find and remove all files which name end with ':Zone.Identifier'
```
find . -type f -name '*:Zone.Identifier' -exec rm f '{}' \;
```
