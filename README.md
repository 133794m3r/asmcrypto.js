asmCrypto
=========

JavaScript implementation of popular cryptographic utilities with performance in mind.

Synopsis
--------

Add `<script src="path/to/asmcrypto.js"></script>` into your page.

    // Hash whole string at once
    digest = asmCrypto.SHA256.hex("The quick brown fox jumps over the lazy dog");

Index
-----

* [Download] (#download)
* [Build & Test](#build--test)
* [Performance](#performance)
* [API Reference](#api-reference)
    * [Message Digest](#sha256)
        * [SHA256](#sha256)
    * [Hash-based Message Authentication](#hmac)
        * [HMAC-SHA256](#hmac_sha256)
    * [Password-based Key Derivation](#pbkdf2)
        * [PBKDF2-HMAC-SHA256](#pbkdf2_hmac_sha256)
    * [Block Cipher](#aes)
        * [AES](#aes)
    * [Asymmetric encryption](#rsa)
        * [RSA](#rsa)
* [Bugs & TODO](#bugs--todo)
* [Donate](#donate)

Download
--------

* [Compressed JS file](http://vibornoff.com/asmcrypto.js) 67KB,
* [Source Map file](http://vibornoff.com/asmcrypto.js.map) 33KB,
* [All-in-One archive](http://vibornoff.com/asmcrypto.tar.gz) 56KB.

Build & Test
------------

Before you start check that [Grunt](http://gruntjs.com/) is installed:

    grunt --version

Then download and build the stuff:

    git clone https://github.com/vibornoff/asmcrypto.js.git
    cd asmcrypto.js/
    git submodule update --init --recursive
    npm install
    grunt

After build is complete open `test.html` in your browser and check that all tests are passed.

Congratulations! Now you have your `asmcrypto.js` and `asmcrypto.js.map` ready to use ☺

Performance
-----------

In the development of this project, special attention was paid to the performance issues.
In the result of all the optimizations made this stuff is pretty fast under Firefox and Chrome.

My *Intel® Core™ i7-3770 CPU @ 3.40GHz* typical processing speeds are:
* *Chrome/31.0*
    * SHA256: 51 MiB/s (**9 times faster** than *SJCL* and *CryptoJS*)
    * AES-CBC: 47 MiB/s (**13 times faster** than *CryptoJS* and **20 times faster** than *SJCL*)
* *Firefox/26.0*
    * SHA256: 144 MiB/s (**5 times faster** than *CryptoJS* and **20 times faster** than *SJCL*)
    * AES-CBC: 81 MiB/s (**3 times faster** than *CryptoJS* and **8 times faster** than *SJCL*)

See benchmarks:
* [SHA256](http://jsperf.com/sha256/34),
* [HMAC-SHA256](http://jsperf.com/hmac-sha256/1),
* [PBKDF2-HMAC-SHA256](http://jsperf.com/pbkdf2-hmac-sha256/2),
* [AES](http://jsperf.com/aes).

API Reference
-------------

### SHA256

[Secure Hash Algorithm](http://en.wikipedia.org/wiki/SHA-2) — a cryptographic hash function with 256-bit output.

#### SHA256.BLOCK_SIZE = 64

#### SHA256.HASH_SIZE = 32

#### SHA256.hex( data )

Calculates message digest of the supplied input `data` (can be a binary string or `ArrayBuffer`/`Uint8Array` object).

Returns a string containing hex-encoded message digest.

Throws
* `TypeError` when something ridiculous is supplied as input data.

#### SHA256.base64( data )

Calculates message digest of the supplied input `data` (can be a binary string or `ArrayBuffer`/`Uint8Array` object).

Returns a string containing hex-encoded message digest.

Throws
* `TypeError` when something ridiculous is supplied as input data.

### HMAC

[Hash-based Message Authentication Code](http://en.wikipedia.org/wiki/HMAC)

Used to calculate message authentication code with a cryptographic hash function
in combination with a secret cryptographic key.

#### HMAC_SHA256.BLOCK_SIZE = 64

#### HMAC_SHA256.HMAC_SIZE = 32

#### HMAC_SHA256.hex( password, data )

Calculates HMAC-SHA256 of `data` with `password`. Both can be either binary strings or `Uint8Array`/`ArrayBuffer` objects.

Returns a string containing hex-encoded message authentication code.

Throws
* `TypeError` when something ridiculous is supplied as input data.

#### HMAC_SHA256.base64( password, data )

Calculates HMAC-SHA256 of `data` with `password`. Both can be either binary strings or `Uint8Array`/`ArrayBuffer` objects.

Returns a string containing base64-encoded message authentication code.

Throws
* `TypeError` when something ridiculous is supplied as input data.

### PBKDF2

[Password-Based Key Derivation Function 2](http://en.wikipedia.org/wiki/PBKDF2)

Applies a cryptographic hash function to the input password or passphrase along with a salt value and repeats the process many times to produce a derived key,
which can then be used as a cryptographic key in subsequent operations. The added computational work makes password cracking much more difficult.

#### PBKDF2_HMAC_SHA256.bytes( password, salt, iterations, dklen )

Derive key from the `password` with `salt`. Both can be either binary strings or `Uint8Array`/`ArrayBuffer` objects.

Optional `iterations` (number of key derivatoin rounds) and `dklen` (desired key length) may be supplied.

Throws
* `TypeError`.

#### PBKDF2_HMAC_SHA256.hex( password, salt, iterations, dklen )

The same as above except returning value type.

#### PBKDF2_HMAC_SHA256.base64( password, salt, iterations, dklen )

The same as above except returning value type.

### AES

Advanced Encryption Standard

#### AES.BLOCK_SIZE = 16

#### AES_CBC

Cipher block chaining mode.

##### AES_CBC.encrypt( data, key, padding, iv )

Encrypts supplied `data` with `key` in CBC mode. Both can be either binary strings or `Uint8Array` objects or `ArrayBuffer` objects.

Optional `padding` and `iv` may be passed to override default settings (PKCS#5 padding is on and iv is zero-vector).

Returns encrypted data as `Uint8Array`.

##### AES_CBC.decrypt( data, key, padding, iv )

Decrypts supplied `data` with `key` in CBC mode. Both can be either binary strings or `Uint8Array` objects or `ArrayBuffer` objects.

Optional `padding` and `iv` may be passed to override default settings (PKCS#5 padding is on and iv is zero-vector).

Returns encrypted data as `Uint8Array`.

#### AES_CCM

Counter with CBC-MAC mode.

Due to JS limitations (counter is 32-bit unsigned) maximum encrypted message length is limited to near 64 GiB ( 2^36 - 16 ) per `nonce`-`key` pair.

Additional authenticated data `adata` maximum length is limited to 65279 bytes ( 2^16 - 2^8 ),
wich is considered enough for the most of use-cases.

Optional `tagSize`, the size of the authentication tag, may be 4, 6, 8, 12, 16 (default).

Keep in mind that **same nonce must not be used more than once with the same key**.

##### AES_CCM.encrypt( data, key, nonce, adata, tagsize )

Encrypts supplied `data` with `key`-`nonce` in CCM mode.

Returns encrypted data as `Uint8Array`.

##### AES_CCM.decrypt( data, key, nonce, adata, tagsize )

Decrypts supplied `data` with `key`-`nonce` in CCM mode.

Returns encrypted data as `Uint8Array`.

### RSA

#### RSA.generateKey( bitlen, pubexp )

Generate RSA private key of `bitlen` length along with the public exponent `pubexp`.

Run 50 rounds of Miller-Rabin test for each prime candidate.

#### RSA_OAEP_SHA256.encrypt( data, key, label )

TODO

#### RSA_OAEP_SHA256.decrypt( data, key, label )

TODO

#### RSA_PSS_SHA256.sign( data, key, slen )

TODO

#### RSA_PSS_SHA256.verify( signature, data, key, slen )

TODO

Bugs & TODO
-----------

* Progressive operations are temporary fade out, they'll be back with WebCrypto API;
* Moar docs needed ☺

Not yet implemented:
* aes-gcm,
* sha1,
* scrypt,
* dsa, ecdsa.

Donate
------

If you like this stuff feel free to donate some funds to `1CiGzP1EFLTftqkfvVtbwvZ9Koiuoc4FSC` ☺
