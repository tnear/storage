# Self Encrypting Drives

A self-encrypting drive (SED) implements all the features above. Every SED contains a Media Encryption Key (MEK), also known as Data Encryption Key (DEK). Data is encrypted on writes using AES.

See also: [Opal](OpalStorageSpecification.md)

## Taking ownership
Taking ownership is how a drive goes from "anyone can configure it" to "only you can configure it".

## Drive locking
Drive locking is an access control layer that sits on top of encryption. When a locking range is *locked*, no I/O can be performed (reads return errors, and writes are rejected).

### Locking ranges
Drives allow locking the entire drive (Global Range, *LR0*), or individual blocks.

## Security provider
A security provider (SP) is a collection of security-related state inside a drive's firmware.

### Admin SP
This SP is used to take drive ownership and setting master credentials.

### Locking SP
This SP holds lock/unlock state for data ranges.
