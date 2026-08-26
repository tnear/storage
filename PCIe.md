# PCIe

PCI Express (Peripheral Component Interconnect Express) is a high-speed standard used to connect hardware components inside computers.

See also: [`SR-IOV`](SRIOV.md)

## PCIe function

A *function* is an independently discoverable PCIe interface.

The word function comes from BDF: Bus, Device, Function. Ex: `0000:01:00.X`, where `X` is the function digit. Certain physical cards have multiple functions, ex: a combination card with sound and a modem.

```
0000:03:00.1
^^^^ ^^ ^^ ^
 |    |  | └─ function 1
 |    |  └─── device 0
 |    └────── bus 3
 └─────────── PCI domain/segment 0
```

Classic PCI uses three bits for functions (`0-7`), which is insufficiently low for SR-IOV.

### Physical function

The *physical function* (PF) is `0`. This always exists for a device.

### Virtual function

*Virtual functions* are created on demand by the PF.

To get past the old 8-function cap, SR-IOV relies on a PCIe extension called ARI (Alternative Routing-ID Interpretation), which allows hundreds of VFs. This allows, for example, one SSD to present many VFs.
