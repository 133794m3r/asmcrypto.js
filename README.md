asmCrypto
=========

JavaScript implementation of popular cryptographic utilities with performance in mind.

[UglifyJS2](https://github.com/mishoo/UglifyJS2) is required to build this.

Synopsis
--------

Add `<script src="path/to/asmcrypto.js"></script>` into your page.

    // Hash whole string at once
    digest = asmCrypto.SHA256.hex("The quick brown fox jumps over the lazy dog");

    // Or hash it chunk-by-chunk
    hash = new asmCrypto.SHA256;
    hash.process("The quick brown");
    hash.process(" fox jumps over the ");
    hash.process("lazy dog");
    digest = hash.finish().asHex(); // also you can chain method calls

Index
-----

* [Build & Test](#build--test)
* [API Reference](#api-reference)
    * [SHA256](#sha256)
    * [HMAC](#hmac)
    * [HMAC-SHA256](#hmac_sha256)
    * [PBKDF2](#pbkdf2)
    * [PBKDF2-HMAC-SHA256](#pbkdf2_hmac_sha256)
    * [AES](#aes)
* [Performance](#performance)
* [Bugs & TODO](#bugs--todo)
* [Donate](#donate)

Build & Test
------------

Before you start check that [UglifyJS2](https://github.com/mishoo/UglifyJS2) is installed:

    uglifyjs -V

Then download and build the stuff:

    git clone https://github.com/vibornoff/asmcrypto.js.git
    cd asmcrypto.js
    make

After build is complete open `test.html` in your browser and check that all tests are passed.

Congratulations! Now you have your `asmcrypto.js` and `asmcrypto.js.map` ready to use ☺

API Reference
-------------

### SHA256

Implementation of Secure Hash 2 algorithm with 256-bit output length.

#### Static methods and constants

##### SHA256.BLOCK_SIZE = 64

##### SHA256.HASH_SIZE = 32

##### SHA256.hex(input)

Same as `staticInstance.reset().process(input).finish().asHex()` (see below).

##### SHA256.base64(input)

Same as `staticInstance.reset().process(input).finish().asBase64()` (see below).

#### Constructor

##### SHA256(options)

Constructs new instance of `SHA256` object.

Optional `options` object can be passed. When ommited, next defaults are used:

    {
        heapSize: 4096  // must be a multiple of 4096 as asm.js requires
    }

#### Methods and properties

##### sha256.BLOCK_SIZE = 64

##### sha256.HASH_SIZE = 32

##### sha256.reset()

Resets internal state into initial.

##### sha256.process(input)

Updates internal state with the supplied `input` data.

Input data can be a string or instance of `ArrayBuffer` or `ArrayBufferView`.

Throws
* `new Error("Illegal state")` when trying to update `finish`'ed state,
* `new ReferenceError("Illegal argument")` when something ridiculous is supplied as input data.

##### sha256.finish()

Finishes hash calculation.

Throws
* `new Error("Illegal state")` when trying to finish already `finish`'ed state,

##### sha256.asHex()

Returns string representing hex-encoded message digest.

Throws
* `new Error("Illegal state")` when trying to get non-`finish`'ed state.

##### sha256.asBase64()

Returns string representing base64-encoded message digest.

Throws
* `new Error("Illegal state")` when trying to get non-`finish`'ed state.

##### sha256.asArrayBuffer()

Returns raw message digest as an `ArrayBuffer` object.

Throws
* `new Error("Illegal state")` when trying to get non-`finish`'ed state.

##### sha256.asBinaryString()

Returns raw message digest as a binary string.

Throws
* `new Error("Illegal state")` when trying to get non-`finish`'ed state.

### HMAC

TODO

### HMAC_SHA256

TODO

### PBKDF2

TODO

### PBKDF2_HMAC_SHA256

TODO

### AES

The Advanced Encryption Standard

#### Static methods and constants

##### AES.BLOCK_SIZE = 16

##### AES.encrypt( data, key, options )

Shorthand for `staticAesCbcInstance.reset(key, options).encrypt(input).result`.

Encrypts supplied `data` with `key` in CBC mode. Both can be either binary strings or `Uint8Array` objects or `ArrayBuffer` objects.

Additional `options` object can be passed to overried default settings ([see below](#aesencrypt-data-options-)).

Returns encrypted data as `Uint8Array`.

##### AES.decrypt( data, key, options )

Shorthand for `staticAesCbcInstance.reset(key, options).dencrypt(input).result`.

Decrypts supplied `data` with `key` in CBC mode. Both can be either binary strings or `Uint8Array` objects or `ArrayBuffer` objects.

Additional `options` object can be passed to overried default settings ([see below](#aesdecrypt-data-options-)).

Returns decrypted data as `Uint8Array` object.

#### Constructors

##### ECB_AES( key, options )

Constructs an instance of AES cryptor in ECB mode.

**Attention! Beware of using this mode to encrypt low-enthropy data. Read [wiki article about ECB mode](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_.28ECB.29) before you decide to use it.**

If `key` is supplied creates new key schedule, `key` can be either a binary string or `Uint8Array` object or `ArrayBuffer` object.

Additional `options` object can be passed:
* `heapSize` — asm.js heap size to allocate for cryptor, must be multiple of 4096, default is 4096;
* `padding` — boolean value to turn on/off PKCS#7 padding, default is `true`.

##### CBC_AES( key, options )

Constructs an instance of AES cryptor in CBC mode.

If `key` is supplied creates new key schedule, `key` can be either a binary string or `Uint8Array` object or `ArrayBuffer` object.

Additional `options` object can be passed:
* `heapSize` — asm.js heap size to allocate for cryptor, must be multiple of 4096, default is 4096;
* `iv` — initialization vector to be used, binary string/`Uint8Array`/`ArrayBuffer`, when ommited default all-zero-value is used;
* `padding` — boolean value to turn on/off PKCS#7 padding, default is `true`.

#### Methods and properties

##### aes.BLOCK_SIZE = 16

##### aes.KEY_SIZE = 16

##### aes.result

Encryption/decryption operation result as `Uint8Array` object.

##### aes.reset( key, options )

Reset internal state. Both arguments are optional.

If `key` is supplied, creates new key schedule.

If either `options.iv` or `options.padding` is supplied, replaces old/default values to new ones.

Returns `this` object so method calls could be chained.

Throws
* `ReferenceError("Illegal argument")` in case of bizzarie stuff supplied instead of key
* `Error("Illegal key size")` — obvious ☺

##### aes.encrypt( data, options )

Encrypts the supplied `data`, it can be either binary string or `Uint8Array` object or `ArrayBuffer` object.

Additional `options` object can be passed to override `iv` or `padding` settings.

Returns `this` object so method calls could be chained.

Throws
* `ReferenceError("Illegal argument")` in case of bizzarie stuff supplied instead of data
* `Error("Data length must be multiple of …")` when padding is turned off and data length isn't multiple of block size

##### aes.decrypt( data, options )

Decrypts the supplied `data`, it can be either binary string or `Uint8Array` object or `ArrayBuffer` object.

Additional `options` object can be passed to override `iv` or `padding` settings.

Returns `this` object so method calls could be chained.

Throws
* `ReferenceError("Illegal argument")` in case of bizzarie stuff supplied instead of data
* `Error("Data length must be multiple of …")` when data length isn't multiple of block size

Performance
-----------

This stuff is pretty fast under Firefox and Chrome.

See benchmarks:
* [SHA256](http://jsperf.com/sha256/30),
* [HMAC-SHA256](http://jsperf.com/hmac-sha256/1),
* [PBKDF2-HMAC-SHA256](http://jsperf.com/pbkdf2-hmac-sha256/2),
* [AES](http://jsperf.com/aes).

Bugs & TODO
-----------

* PBKDF2-HMAC-SHA256: probable OOB write during first iteration of a block

Not yet implemented:
* aes: ctr, ccm, gcm,
* scrypt,
* rsa, dsa, ecdsa.

Donate
------

If you like this stuff feel free to donate some funds to:
* My Bitcoin address `1CiGzP1EFLTftqkfvVtbwvZ9Koiuoc4FSC`
* [My Flattr account](https://flattr.com/submit/auto?user_id=vibornoff&url=https%3A%2F%2Fgithub.com%2Fvibornoff%2Fasmcrypto.js&title=asmCrypto.js&language=en_US&&category=software)
