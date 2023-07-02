# HashPasswords

Package that allows hashing a password.

This includes a function that creates a randomized salt.

Example:

 ```
       salt1←HashPasswords.CreateSalt 
       hash1←HashPasswords.Hash salt1,'123456'
       salt2←HashPasswords.CreateSalt
       ⎕←hash2←HashPasswords.Hash salt2,'123456'
       hash1≢hash2
 1
       hash1≡HashPasswords.Hash salt1,'123456'
 1
       hash2≡hash2←HashPasswords.Hash salt2,'123456'
 1
 ```