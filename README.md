# HashPasswords

Salting and hashing passwords in Dyalog APL.

## Overview

`HashPasswords` creates random salts and computes SHA-256 hashes, so that an application can
store passwords without ever keeping them in clear text. It runs on Windows, Linux and macOS.

It is available as a [Tatin](https://tatin.dev) package. As a project it is managed by
[Cider](https://github.com/aplteam/Cider "Link to Cider, a project manager"), the Dyalog APL
project manager.

## Requirements

* Dyalog APL 18.2 or later
* The Tatin package manager, which ships with Dyalog from 19.0 onwards
* On Linux and macOS: the Conga shared library — see [Conga libraries](#conga-libraries)

## Installation

```apl
      ]Tatin.LoadPackages H@aplteam-HashPasswords
```

This makes the package available as `H`, which is the name used in the examples below.

## API

| Function | Description |
|---|---|
| `CreateSalt n` | Returns a random salt of `n` characters, drawn from `a-z`, `A-Z` and `0-9` |
| `Hash text` | Returns the SHA-256 hash of `text`, encoded as UTF-8, as 64 lowercase hex digits. Calls `Init_SHA256` when needed |
| `Init_SHA256 ⍬` | Prepares for hashing. Does nothing when that has already been done, unless `1` is passed as `⍺`. Returns a shy `1` |
| `Dispose` | Releases what `Init_SHA256` set up |

Calling `Init_SHA256` yourself is optional: `Hash` takes care of it.

## Usage

To store a password, create a salt, hash the salt together with the password, and keep both:

```apl
      salt←H.CreateSalt 16
      hash←H.Hash salt,'MySecretPassword'
```

To check a password entered later, hash it with the stored salt and compare the result with
the stored hash:

```apl
      hash≡H.Hash salt,'MySecretPassword'
1
      hash≡H.Hash salt,'mysecretpassword'
0
```

The same input always produces the same hash, on every platform:

```apl
      H.Hash 'aplteam-api-key'
7b15602836c9f4e75fc886bea989973e634ca62771800fdfe1722d1c995000cf
```

## Why salting?

* Every user gets their own salt, so two users with the same password end up with different
  hashes. An attacker cannot tell that they share a password.
* A password that is too short to be secure by normal standards becomes secure anyway — at
  least as far as its length is concerned.

Salting a password with a randomly generated salt therefore increases security.

The same password hashed with two different salts gives two different hashes, and each one
can be reproduced with its own salt:

```apl
      salt1←H.CreateSalt 16
      salt2←H.CreateSalt 16
      hash1←H.Hash salt1,'123456'
      hash2←H.Hash salt2,'123456'
      hash1≢hash2
1
      hash1≡H.Hash salt1,'123456'
1
      hash2≡H.Hash salt2,'123456'
1
```

The salt must be stored, because checking a password later needs it — and it must be stored in
clear text. No, that is not a security risk!

For more information see <https://en.wikipedia.org/wiki/Salt_(cryptography)>.

## Conga libraries

On Windows, `HashPasswords` uses .NET; both .NET and .NET Framework are supported.

On Linux and macOS it calls the SHA-256 functions in the Conga shared library
`libconga??ssl64.so` (Linux) or `libconga??ssl64.dylib` (macOS), where `??` stands for the
Conga version, for example `34`. A 32-bit interpreter uses `libconga??ssl32.*` instead.

By default the library is taken from the `lib/` folder of the Dyalog installation that is
running. When that is not suitable — for example, an application that ships a runtime
interpreter with a limited set of Dyalog files, on a machine without Dyalog installed — copy
the library into a folder of your own and point `PathToCongaDLLs` at it before the first call
to `Hash` or `Init_SHA256`. The path must end with a slash:

```apl
      H.##.PathToCongaDLLs←'/path/to/folder/'
```

`H` refers to the package's API, and the variable lives in the namespace above it.

## Development

The project is managed by Cider. Once it is open:

* `#.HashPasswords.TestCases.RunTests` runs the test suite
* `#.HashPasswords.Admin.Make 1` builds a new version of the package into `Dist/`

Test 900 compares the hashes produced on Windows, Linux and macOS. It can only run after the
test suite has been run on all three platforms against the same project folder.

## License

MIT — see [LICENSE](LICENSE).
