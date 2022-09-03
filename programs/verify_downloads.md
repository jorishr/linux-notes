# Verify downloaded files
# Checksum
To avoid corrupted files after via unreliable file transmission you can complete a byte check with `cksum`. The original file has been run through an algorithm with a unique outcome, a hash. Compare the hash given by the author to hash generated on your local device. A more robust algorithm is provides by the SHA265sum program.
```bash
cksum <filename>    # outputs the hash for visual inspection

SHA265sum <file>
```

## PGP Verification
With checksum you can be assured that the file that has been downloaded is the same file provided by the server. But, the server may have been hacked and the both the file and the hash published on the website may be different from the original. To mitigate this attack vector the author(s) of a file can sign the original file with their PGP keys. The process is relatively simple:

1. You download the public key (.asc file) of the software author.
2. Check the public key’s fingerprint to ensure that it’s the correct key.
3. Import the correct public key to your GPG public keyring.
4. Download the PGP signature file (.sig) of the software.
5. Use public key to verify PGP signature. If the signature is correct, then the software wasn’t tampered with.

Each website may have slightly different instructions.

This still requires you to trust that the author's PGP signatures really belong to that person. There are public directories and people may share their public keys on social media or in private.

- reference: [GnuPrivacyGuardHowto](https://help.ubuntu.com/community/GnuPrivacyGuardHowto)