# VFIO

VFIO (Virtual Function I/O) is a Linux kernel framework that allows direct access to hardware from user-space, most commonly used for device passthrough to virtual machines.

VFIO lets user-space programs (like QEMU) own and control real hardware devices directly. This yields near-native speed with no emulation overhead.

See also: [PCIe](PCIe.md), [`uio_pci_generic`](UioPciGeneric.md)

## Why VFIO exists
Traditionally, device drivers run in the kernel. VFIO changes that by moving device control into user-space while keeping security and isolation via the kernel.

## Use-cases

### PCI passthrough
This allows assigning a CPU or NIC directly to a VM:
```
Host -> VFIO -> QEMU -> VM -> hardware device
```

### High-performance user-space drivers
Applications can use VFIO to bypass kernel networking and storage stacks and access hardware directly.

Examples: DPDK, SPDK

## Components
1. [IOMMU](MemoryManagementUnit.md#iommu-inputoutput-memory-management-unit): VFIO relies on IOMMU for safe DMA access
1. VFIO kernel modules: `vfio`, `vfio_pci`, `vfio_iommu_type1`
1. `/dev/vfio` interface: user-space interacts via `/dev/vfio/<group>`

## How VFIO works
1. Identify a device using `lspci`
1. Bind it to VFIO: `echo <device_id> > /sys/bus/pci/drivers/vfio-pci/bind`. This creates `/dev/vfio/<group>`
1. QEMU then opens `/dev/vfio/<group>`
1. QEMU sets up DMA via IOMMU
1. VM uses device directly

## `vfio-pci`
`vfio-pci` is a Linux kernel driver that allows a user-space program—or a virtual machine—to control a PCI device safely.

`vfio-pci` is newer and safer than `uio_pci_generic` because it can use an IOMMU to restrict which memory the device may access.

### Devices
VFIO exposes devices through files such as:

```
/dev/vfio/vfio
/dev/vfio/12
```

The numbered file represents an *IOMMU group*, which is a set of devices that the hardware can isolate from other devices.

## Resources
- https://spdk.io/doc/system_configuration.html
