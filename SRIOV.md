# SR-IOV

Single Root Input/Output Virtualization (SR-IOV or SRIOV) is a PCI Express (PCIe) specification that allows a single physical device to be shared by multiple virtual machines (VMs). SR-IOV improves performance and manageability by isolating PCI Express resources.

See also: storage.git (NVMe.md and PCIe.md)

> ### "Single root"
> This means that the physical device and all its PFs and VFs are underneath one hosts's PCIe *root complex*.
> ```
> One physical host
> └── One PCIe root complex
>     └── One physical NVMe SSD
>         ├── PF
>         ├── VF1 → VM A
>         └── VF2 → VM B
>
> Root complex = host-side starting point in PCIe hierarchy.

## The problem
A physical NVMe SSD is one device. If a server is running 20 virtual machines, and each VM needs to talk to that SSD, something has to mediate. The traditional answer is the hypervisor: every I/O from every VM traps into the hypervisor, which forwards it to the real device, then returns the result. This is slow because every single I/O crosses the hypervisor boundary twice.

Ideally, you want each VM to feel like it has its own dedicated NVMe controller (its own queues, register space, and interrupts) so it can submit I/O directly without the hypervisor in the middle.

## How it works
Virtual machines use a *virtual function* to bypass the hypervisor and talk directly to hardware. Data is DMA directly to and from a VM without the hypervisor touching it.

> ### Aside: PCI passthrough
> PCI passthrough is an alternative technique to give VMs direct access to hardware. Its limitation is that the VM uniquely owns the function (SSD). Other VMs cannot access it.

### Physical Function (PF)
A *physical function* is a full-featured PCIe function: the "real" one. It can do everything the device supports: configuration, management, data transfer. It's also the function that controls SR-IOV itself. It tells the hardware "create me 4 virtual functions." The hypervisor owns the physical function.

### Virtual Function (VF)
A *virtual function* is a minimal PCIe function created by the PF. When a VM is handed a VF, it's touching actual hardware, not a fake interface the hypervisor is pretending to be. Its capabilities are intentionally limited. It can do I/O, but it can't reconfigure the device or manage other functions.

A VF is a *real PCIe function*, not a software emulation. It has real registers at a real address. A VM with a VF assigned to it via passthrough is directly talking to hardware for better performance.

Each VF receives its own PCIe identity, ex:
```
0000:41:00.1  # VF1
0000:41:00.2  # VF2
0000:41:00.3  # VF3
```

#### Creating VFs does not divide SSD capacity
Suppose there is an 8 TB SSD. Creating four VFs does *not* automatically produce four 2 TB disks. SR-IOV only creates PCIe interfaces, nothing to do with storage capacity.

Storage capacity is through NVMe namespaces. The administrator must configure which namespaces are accessible through which functions.

### IOMMU provides memory isolation
GIving a VF DMA capability would be dangerous if it could read arbitrary host memory. Instead, the IOMMU associates each PCIe function with permitted mappings, ex:

```
PF DMA  -> host mappings
VF1 DMA -> only VM A's mapped memory
VF2 DMA -> only VM B's mapped memory
```

## Benefits
- Improved performance: SR-IOV allows VMs to have direct hardware access to network resources.
- Lower overhead: SR-IOV has lower overhead than software-based emulation of a network card.
- Run multiple operating systems: SR-IOV allows users to run multiple operating systems on a single piece of hardware without rebooting or changing the operating system.
