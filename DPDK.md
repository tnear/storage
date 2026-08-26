# DPDK

The Data Plane Development Kit (DPDK) provides a set of data plane libraries which offload TCP packet processing from the OS kernel into user space.

DPDK allows user to write applications which have direct access to a NIC instead of going through the OS kernel.

See also: [SPDK](SPDK.md)

## How DPDK Operates

### Kernel bypass
Without DPDK, the NIC sends a packet to the kernel, the kernel copies it, then sends to the receiver. This copying and context switching degrades performance.

```
Without DPDK:
NIC -> [Kernel: data link layer, network layer, transport layer] -> Application

With DPDK:
NIC -> Application
```

### Poll mode driver (PMD)
DPDK is a user-space application and therefore does not have direct access to hardware interrupts. Instead of waiting for hardware interrupts to signal packet arrivals, PMDs continuously check (poll) the NIC for new packets. This eliminates interrupt-handling overhead.

PMDs implement zero-copy techniques where packets stay in the same memory location throughout processing. PMDs use lockless queues and other techniques to minimize synchronization overhead in multi-core systems.

### Environment abstraction layer
The environment abstraction layer (EAL) is responsible for gaining access to low-level resources such as hardware and memory space. It provides a generic interface that hides the environment specifics from the applications and libraries.

## Differences from [SPDK](SPDK.md)
- DPDK is for fast packet processing. It bypasses the kernel's network stack and moves network I/O into userspace.
    - DPDK minimizes network latency.
- SPDK is for fast storage I/O. It bypasses the kernel's storage and moves NVMe I/O into userspace.
    - SPDK minimizes NVMe SSD I/O latency.

## Resources
- https://en.wikipedia.org/wiki/Data_Plane_Development_Kit
- https://doc.dpdk.org/guides/
- "Introduction to DPDK", https://youtu.be/1DWxo2gF-RQ
- "2.6: DPDK Overview", https://youtu.be/0G6u409cSos (not much useful info)
- "Linux Kernel vs DPDK: HTTP Performance Showdown", https://youtu.be/zWes9ea09XE
- https://www.dpdk.org/memory-in-dpdk-part-1-general-concepts/
    - Also parts 2-4
