# Opal Storage Specification

Opal is a security standard for self-encrypting drives (SEDs). It defines how a storage device implements hardware-based encryption and access control independently of the OS.

See also: [self-encrypting drives](SelfEncryptingDrives.md)

## Overview
The drive itself handles encryption. Plaintext is never exposed on the bus. The OS and CPU are not involved.

### Master boot record
MBR is a small data structure stored at the very beginning of a disk that tells the system where to find the bootable partition. When a computer powers on, the BIOS reads the MBR first and hands off execution to the bootloader found there.

## Key concepts
- Locking ranges: subdivide the drive into independently lockable regions, each with its own credentials
- Shadow MBR: a fake MBR presented to the system before authentication. After unlock, the real MBR is revealed
- Pre-boot Authentication (PBA): only after successful authentication does the drive unlock and allow the real OS to boot. This means the encryption key is never loaded into memory until the user proves they're authorized

## Advantages
- Encryption/decryption offloaded to drive hardware: no CPU overhead
- Instant secure erase: just discard the encryption key
- Transparent to the OS: no software encryption stack needed

### Limitations
- Trust is placed entirely in the drive firmware: hard to audit or verify
- Some drives have weak implementations (ex: Samsung vulnerabilities discovered in 2018)

### Common use cases
- Enterprise laptop full-disk encryption
- Compliance-driven data protection (HIPAA, GDPR)
