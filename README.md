# HashPasswords

## Overview

Package that allows hashing a password.

This includes a function that creates a randomized salt.

`HashPasswords` can be consumed as a Tatin package from <https://tatin.dev> as demonstarted in the examples.

As a project, it is managed by the Dyalog APL project manager [Cider](https://github.com/aplteam/Cider "Link to Cider, a project manager")

## Requirements

* Dyalog APL 18.0 or better
* Tatin package manager (part of Dyalog from 19.0 onwards)

## Examples

 ```
       ]LoadPackages H@HashPasswords
...
       ≢salt1←H.CreateSalt 10
10
       hash1←H.Hash salt1,'123456'
       salt2←H.CreateSalt 10
       ⎕←hash2←H.Hash salt2,'123456'
455b43ce21b0a7bd8e1a7c5594d4f034ffe385d5627676465a38f0629da38862 ⍝ (*)
       hash1≢hash2
 1
       hash1≡H.Hash salt1,'123456'
 1
       hash2≡H.Hash salt2,'123456'
 1
 ```

(*) Will be different for you
