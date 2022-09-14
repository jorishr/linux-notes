# Download files
## Download Tools
*wget* is a tool designed to download files from the web (without user interaction). *curl* is more powerful data transfer tool that supports many more protocols and features.
```bash
# download a remote file with same file name in current folder
wget http://www.website-name.com/file
wget -r http://www.website-name.com/file    # download an entire website recursively

# specify an output file name with -O; if -O - is used, you write to standard output
wget http://www.website-name.com/file -O my_file

# download and extract to a folder (no local .tar copy stored)
wget http://www.website-name.com/file -O - | tar -xzv -C /home

# if you do want a local copy of the archive
wget http://www.website-name.com/file && tar -xzv file -C /home
```
# Compress and extract
```bash
# Gunzip
# compres a single file to gunzip .gz format:
gzip <file>         # compresses the file inside the current folder
gunzip <file>       # decompress the .gz file

# For multiple files use tar
# The first tar command parameters: x = extract; c = create; t = list
# tar -czvf <name>.tar <file1> <file2> <file3>
# other parameters: z = use zip format; v = verbose output in terminal; f = file type

tar -czvf archive.tar *
# adds all files inside the folder to the archive 

# Extract all files from archive
# tar -xzvf <name>.tar	
tar -xzvf archive.tar -C /home

# list files inside an archive
tar -tvf <name>.tar
```