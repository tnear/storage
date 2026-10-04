# Virtual File System

The virtual file system (VFS) is a layer inside the OS kernel that sits between programs and the actual file systems. It gives programs one consistent set of operations (`open`, `read`, `write`, `close`, `stat`, etc.).

## Motivation

When you run `cat notes.txt`, the program doesn't care whether the file lives on an SSD formatted as ext4, a USB stick formatted as FAT32, or a network drive.

Without some shared layer, every program would need to know how to talk to every kind of storage.

```
   Your program: open(), read(), write()
                        │
                        ▼
              ┌───────────────────┐
              │        VFS        │   <- one common interface
              └───────────────────┘
               │       │        │
               ▼       ▼        ▼
            ext4     FAT32     NFS    <- real file systems
```

Each real file system (ext4, NTFS, NFS, etc.) is written to fulfill a contract with the VFS. The VFS doesn't know how ext4 finds data on disk. It just calls ext4's `read` function when the file belongs to ext4.
