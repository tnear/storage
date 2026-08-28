# uio_pci_generic

`uio_pci_generic` is a Linux kernel driver that lets a user-space program control a PCI device. "UIO" mans userspace I/O.

See also: [VFIO](VFIO.md)

## Introduction

Normally, a PCI device is controlled by a specialized kernel driver:
```
PCI network card -> kernel network driver -> Linux networking
```

With `uio_pci_generic`, the arrangement is different:

```
PCI device -> uio_pci_generic -> user-space program
```

## How it works

The kernel performs a few essential tasks, such as exposing the device's memory regions and delivering interrupts. But the user-space program implements most of the device logic.

After a device is bound to `uio_pci_generic`, Linux exposes it through files such as:
```
/dev/uio0
/sys/class/uio/uio0/
```

A program can then map the device's PCI memory into its own address space and interact with its hardware registers directly. Frameworks such as DPDK use this technique.

### Consequences

Devices bound to `uio_pci_generic` cease to be managed by the kernel driver. For an SSD, it will no longer appear in `/dev/nvme` and disappear from other tooling.

## Limitations

`uio_pci_generic` is an older technique. For safer DMA access, use `vfio-pci` because it can use an IOMMU to restrict which memory the device may access.

## Resources
- https://spdk.io/doc/system_configuration.html
