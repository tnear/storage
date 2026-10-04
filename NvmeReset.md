# nvme reset

`nvme-reset` - Reset the nvme controller.

See also: [`nvme subsystem-reset`](NvmeSubsystemReset.md)

## Introduction
`nvme reset` triggers a controller-level reset on an NVMe device. It reinitializes the controller without a full power cycle. It's a software equivalent of unplugging and replugging the drive electrically at the controller level rather than the whole subsystem.

`nvme reset` (and `nvme subsystem-reset`) do not erase or modify data. However, any in-flight I/O will fail.

### Use-cases
- Recovering a controller that's hung, without rebooting the host
- Forcing the controller to re-read certain settings after a change
- Clearing transient firmware/controller issues

## `reset` vs `subsystem-reset`
`nvme subsystem-reset` is a stronger command. `nvme reset` only reinitializes the controller. A subsystem-reset goes one level up and resets the entire NVM subsystem, which is closer to a full power-cycle equivalent. It is typically used when a controller-level reset alone doesn't clear the problem.

## Basic usage

```bash
$ nvme reset /dev/nvme0
```
