# SCSI

Small Computer System Interface (SCSI) is a set of standards for physically connecting and transferring data between hard disk drives.

See also: [NVMe](Nvme.md)

## Introduction
SCSI is a way of communicating to storage devices. SCSI can support different connections:
- SAS (serial attached SCSI): replaces parallel SCSI
- Fibre channel
- iSCSI: SCSI commands over TCP networks
- USB storage

## Comparison to NVMe
- SCSI is an older protocol designed when HDDs were the norm. [NVMe](Nvme.md) is newer and designed for SSDs.
- SCSI also uses the terms "initiator" (computer sending storage requests) and "target" (storage system receiving them)
- An NVMe *namespace* is known as Logical Unit Number (LUN). Once the initiator knows the LUN, it can treat it like a block device (read/write to it)

## iSCSI
iSCSI (standardized in 2004) carries SCSI commands over TCP/IP network. Example:
1. Server (initiator) creates TCP connection to remote storage system (target)
1. Server sends SCSI commands over that connection
1. Storage system performs those commands
1. Data is returned over the network

After connecting, the target presents a LUN (Logical Unit Number). To the server's OS, that LUN appears as a *local block device* even though data travels over the network. Ex: `/dev/sdb`.

Applications do not know that `/dev/sdb` is remote. They issue normal filesystem commands to it. The kernel's iSCSI stack turns that I/O into SCSI commands and send them over TCP.

## Resources
- https://en.wikipedia.org/wiki/SCSI
