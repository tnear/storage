# nvme subsystem-reset

`nvme-subsystem-reset` - Reset the nvme subsystem.

See also: [`nvme reset`](NvmeReset.md)

## Introduction

`nvme subsystem-reset` (and `nvme reset`) do not erase or modify data. However, any in-flight I/O will fail.

## Basic usage

```bash
$ nvme subsystem-reset /dev/nvme0
```

## Reset vs subsystem-reset

See [`nvme reset`](NvmeReset.md#reset-vs-subsystem-reset).
