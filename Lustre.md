# Lustre

Lustre is a distributed file system designed for high-performance computing (HPC) clusters and massive supercomputers. The name combines Linux + cluster. Lustre separates metadata services from data services to allow parallel file access and high speeds.

## The problem it solves
HPC clusters run thousands of compute nodes that all need access to the same massive dataset at once. If that data lives on one machine, there are two bottlenecks:
1. Capacity: finite drive bays
1. Bandwidth: a server's network can only push so many GB/s, no matter how many nodes are asking.

## Lustre's central idea
Split the two kinds of work:
- Metadata server (MDS): file names, permissions, and raw data locations. These are small, frequent requests
- Object storage server (OSS): the actual bytes, large, bulk requests handled by many servers.

A client asks the MDS once for a file's layout, then reads/writes data *directly and in parallel* across multiple servers. Large files are *striped* across several servers, so total bandwidth scales by adding more storage servers, not by buying one faster machine.

### Lustre still uses POSIX filesystem
Lustre behaves like an ordinary POSIX filesystem. Operations such as `cp` and `open()` work like normal. Lustre parallelizes I/O behind the scenes. It's built specifically for huge files and heavy concurrent throughput

### Limitations
Lustre does not work well with tiny files, since that stresses metadata rather than bandwidth.

## Locking
Before touching any part of a file, a Lustre client requests a lock from the relevant server (MDS for metadata, OST for the actual data) and only proceeds once it's granted. This lets many clients share the same file safely: each server tracks who currently holds locks on its own resources.

## Resources
- https://en.wikipedia.org/wiki/Lustre_(file_system)
