# Compress files
- a single file:
```
gzip <file>         //-> compress to .gz format
gunzip <file>       //-> decompress content of .gz file
```
- multiple files:
```
tar cvf <name>.tar <file1> <file2> <file3>
//-> c: create; v: verify output in terminal; filetype

tar xvf <name>.tar	
//-> extract files
```