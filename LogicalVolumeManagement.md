# Logical Volume Management

Logical volume management (LVM) is a storage virtualization tool for Linux that adds a flexible abstraction layer between physical hard drives and file systems.

## Traditional storage

In traditional storage (no LVM), a physical disk is divided into partitions, and each partition size is locked at creation time. This is a rigid system and causes two pain points:

1. A partition can't grow past a disk. Ex: if `/dev/sda1` fills, users cannot easily add more space
1. A partition cannot span multiple disks. There is no way to pool their storage together.

## LVM breakdown

LVM acts as a flexible layer between physical disks and filesystems so that storage can be resized, combined, and moved without rigid boundaries.

LVM consists of three layers (from bottom to top)
1. Physical volume (PV)
1. Volume group (VG)
1. Logical volume (LV)

### Physical volume

A PV is a whole disk or partition managed by LVM. Ex: `pvcreate /dev/sdb` doesn't erase or reformat the disk. Instead, it adds a small LVM label so the disk is recognized as "available for pooling".

### Volume group

A VG is a pool made of one or more PVs. Ex: once both `/dev/sdb` and `/dev/sdc` are both in `vg_data`, LVM stops caring which physical disk any byte lives on. The VG's total size is the sum of its PVs' capacity. To add more storage to pool, simply add another PV to the VG.

### Logical volume

A LV is a slice carved from the VG. This is what gets a filesystem and gets mounted. A LV behaves like a partition except it isn't tied to a physical disk boundary, and can be resized later.
