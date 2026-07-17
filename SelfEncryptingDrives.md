# Self Encrypting Drives

A self-encrypting drive (SED) implements all the features above. Every SED contains a Media Encryption Key (MEK), also known as Data Encryption Key (DEK). Data is encrypted on writes using AES.

See also: [Opal](OpalStorageSpecification.md)

## Drive locking
Drive locking is an access control layer that sits on top of encryption. When a locking range is *locked*, no I/O can be performed (reads return errors, and writes are rejected).

### Locking ranges
Drives allow locking the entire drive (Global Range, *LR0*), or individual spans of blocks.

## Security provider
A security provider (SP) is a collection of security-related state inside a drive's firmware.

### Admin SP
This SP is used to take drive ownership and setting master credentials.

The Security Identifier (SID) lives inside Admin SP. It controls ownership for the entire drive. Its credential starts out equal to MSID (factory-default value printed on the label). "Taking ownership" means authenticating with SID so that only you can unlock it.

The SID has two primary jobs:
1. Activate the Locking SP
1. Revert the drive (factory reset)

#### PSID
The PSID (Physical Secure ID) is a long, random string printed on the drive. It is typically used when the PIN (password) is lost.

Authenticating with the PSID triggers a full cryptographic revert: the drive discards its current MEK, generates a new one, and resets Admin SP and Locking SP back to factory state (SID reverts to equaling MSID again, all Locking Ranges and users are wiped).

### Locking SP
This SP holds lock/unlock state for data ranges. It holds *locking ranges*: spans of blocks, each with their own read/write lock flags.

## Lock on reset
Authentication should not be permanent because that mostly defeats the purpose. If a drive was unlocked and stolen, an attacker would have access to it.

*LockOnReset* is a list of events that cause a range to return to locked (these events are independent of anyone calling "lock").

A power cycle is the most common event to return a drive to locked. The drive's unwrapped MEK is only held in volatile memory. Therefore, losing power destroys that key, and the drive becomes locked when it regains power.

The global range (LR0, the entire drive) typically defaults to LockOnReset.
