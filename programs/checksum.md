# Checksum Files
To avoid man-in-the-middle attacks and corrupted files. The original file has been run through an algorithm with a unique outcome, a hash. 

We do the same with the downloaded file and pipe the result to a grep command for comparison with the hash string given on the download page.
```
SHA265sum <file> | grep -i <hash>
SHA1sum <file> | grep -i <hash>
```