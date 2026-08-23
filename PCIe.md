# PCIe

PCI Express (Peripheral Component Interconnect Express) is a high-speed standard used to connect hardware components inside computers.

## PCIe function

The word *function* comes from BD: Bus, Device, Function. Ex: `0000:01:00.X`, where `X` is the function digit. Certain physical cards have multiple functions, ex: a combination card with sound and a modem.

Classic PCI caps it a 8 functions (`0-7`), which is insufficiently low for SR-IOV.

### Physical function

The *physical function* (PF) is `0`. This always exists for a device.

### Virtual function

*Virtual functions* are created on demand by the PF.

To get past the old 8-function cap, SR-IOV relies on a PCIe extension called ARI (Alternative Routing-ID Interpretation), which allows hundreds of VFs. This allows, for example, one SSD to present many VFs.

