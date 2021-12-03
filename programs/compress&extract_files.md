# Archive file(s)
- compres a single file to gunzip format:
```
gzip <file>         //-> compress to .gz format
gunzip <file>       //-> decompress content of .gz file
```
- archive multiple files using tar program:
```
tar -czvf <name>.tar <file1> <file2> <file3>
//-> c: create; z: use zip format; v: verbose output in terminal; filetype

tar -czvf archive.tar *
//-> to add all files inside the folder to the archive 

tar -czvf archive.tar --remove-files
//-> remove files once archive is created
```
- extract all files from archive
```
tar -xzvf <name>.tar	
//-> extract files
```
- view files inside an archive
```
tar -tvf <name>.tar
```