# RDMA

RDMA extends direct memory access over the network. It provides extremely low latency and high throughput network communication. Additionally, RDMA frees the CPU to do other work by bypassing it.

## Terminology
- In-band: the primary data path where RDMA moves data between regions. This must be as fast as possible. Anything which doesn't need to be in-band is moved out-of-band.
- Out-of-band: a separate channel for connection setup, authentication, error handling, and management. Out-of-band sets up the connection before sending data, exchanges memory addresses to know where to read/write, and handles errors.

## Architecture
### RNIC
The RNIC (remote NIC) has a pointer to local memory and a remote location and it moves that data without interaction by the CPU.

### Queue-pairs (QP)
Queue pairs are used by the application to submit operations to the RNIC. A queue pair consists of a send-queue (SQ) and receive-queue (RQ).

There are four QP types:
1. Reliable connection (RC): most common (comparable to TCP)
1. Unreliable connection (UC)
1. Reliable datagram (RD)
1. Unreliable datagram (UD): comparable to UDP

### Processing completion queues (CQs)
There are two processing methods for CQs:
- Polling mode: low latency, high throughput. CPU intensive.
- Interrupt mode: reduces CPU usage and power. Higher latency.

### Memory region (MR)
A memory region is an area of memory registered with the RDMA hardware (RNIC). This allows direct access for data transfer operations. Upon registration, the RNIC returns two keys:
1. L-KEY: the key for the Local application
1. R-KEY: the key for the Remote application

These uniquely identify memory for reading and writing via RDMA.

## Verbs API
RDMA Verbs API is the programming interface to interact with RDMA-capable hardware. `libibverbs` implements the verbs API. The Verbs API is the same for all protocols (ex: InfiniBand, Ethernet, iWARP [which is over TCP], etc.).

"Verbs" are the commands you can use to control RDMA data transfers. The most common verbs are:
- Memory registration verbs. These register memory regions to be used with RDMA
- Connection verbs. These create QPs that serve as communication endpoints. These also create completion queues (CQs) to receive notifications.
- Data transfer verbs. These include SEND/RECEIVE, WRITE, READ, and ATOMIC
- Completion handling verbs. These poll for completed operations.

## Relationship with [NVMe-OF](NVMe-oF.md)
NVMe-oF can use RDMA as one of its transport options. RDMA was the first transport defined for NVMe-oF and offers the best performance characteristics.

## Protocols

![](images/StorageProtocols.jpg)

### RoCE
See [RoCE](RoCE.md)

### iWARP
See [iWARP](iWARP.md)

### InfiniBand
Uses neither TCP or UDP. Instead, it uses LRH and GRH headers.

## Resources
- "Everything You Wanted to Know About RDMA But Were Too Proud to Ask", https://youtu.be/6t041Lr5FCY
- "What is RDMA and RoCE? SmartNICs explained.", https://youtu.be/xNkCKHf58wE
- "What is RDMA over Converged Ethernet (RoCE)?", https://youtu.be/dLw5bA5ziwU
- "RDMA & RoCEv2 EXPLAINED: The Secret High-Speed Network Behind AI & HPC", https://youtu.be/nKz92Yr09q8
- "Learn RDMA Programming: NVIDIA’s Guide to High-Performance Networking", https://youtu.be/pdCP-35aUlM
