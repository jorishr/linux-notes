# Checksum Files
To avoid man-in-the-middle attacks and corrupted files after data transmission. The original file has been run through an algorithm with a unique outcome, a hash. 

We do the same with the downloaded file and pipe the result to a grep command for comparison with the hash string given on the download page.
```bash
SHA265sum <file> | grep -i <hash>
SHA1sum <file> | grep -i <hash>

#another simpler option
cksum <filename>
```

Check last time a file was modified
```bash
ls -lrt <filename>
ls -lrt | grep <filename>
```