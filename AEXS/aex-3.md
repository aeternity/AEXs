# AEX-3

```
AEX: 3
Title: Secret storage format
Author: Sascha Hanse <shanse@posteo.de>, Shubhendu Shekhar (@shekhar-shubhendu)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-3-secure-storage-format/3220
Status: Review
Type: Informational
Created: 2019-04-03
```

## Simple Summary

The document describes and defines the secret storage approach used by æternity.

## Motivation

The motivation for the AEX is to describe a standard way that is being used by æternity for secret storage.

The secret storage specification, although is being currently used for secret-key storage, should not be limited to it and should be used as a standard way of storing secret text/plain text (esp. user related or user-owned) in an encrypted format.

Having a standard way for encryption and storage of data enables

- Interoperability, not only between æternity and æpps but also between the æpps.
- Easy migration from an aeternity-supported `wallet` to another.

## Specification

- The data should always be stored in a `.json` file.
- Each file should have a minimum of 1 JSON object with the following `required` fields

  - `secret_type` specifies the type of the encrypted data.
  - (optional) `secret_format` specifies the format of the encrypted data,
    if not specified then a consumer should assume raw bytes
  - `symmetric_alg` specifies the algorithm used for symmetric encryption of the secret. This should be authenticated encryption and the only option is `xsalsa20-poly1305` currently.
  - The `ciphertext` is the output of `symmectric_alg` , i.e. the output of libsodiums `crypto_secretbox_easy`, which is `MAC + CIPHER` with an upper-limit of `512 bytes`.
  - `cipher_params` params used for successful decryption of the ciphertext
  - `kdf` specifies the methods used for key derivation.
  - `kdf_params` are the params used by the `kdf`
  - `id` is a Version 4 UUID
  - `version` currently `1`. Defines the version of secret storage format.

- Each JSON Object can also have an optional `name` field, which can be a human-readable name for the secret.

### Secret types

Specifying an appropriate `secret_type` helps consumers to decide the proper way
of handling the decrypted data without having to store additional metadata.

The following `secret_type` values have been proposed:

- `ed25519-bip39-mnemonic`
- `ed25519-slip0010-masterkey`

This list should be expanded with adoption.

It is not advised to use this format as a store for arbitrary binary data.

### Example

```json
{
  "crypto": {
    "secret_type": "ed25519-slip0010-masterkey",
    "symmetric_alg": "xsalsa20-poly1305",
    "ciphertext":"66891af8a59e83f0c600435a0681413644588f296240ab922ee357fa5ffa857f2709f8753b2b70d35625203adc6bf6e8",
    "cipher_params": {
      "nonce": "b085597ac8351330b469e9845fc9fb8cefa07e51cb7736a"
    },
    "kdf": "argon2id",
    "kdf_params": {
      "memlimit_kib": 65536,
      "opslimit": 3,
      "salt": "somesaltyness",
      "parallelism": 1,
    }
  },
  "id": "b1ff1e48-4e3e-4caf-818e-c8a7c3559d97",
  "name": "main",
  "version": 1
}
```

## Reference

- https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition
