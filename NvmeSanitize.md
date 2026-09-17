# nvme-sanitize

`nvme-sanitize` - Send NVMe Sanitize Command, return result

See also: [`nvme-sanitize-log`](NvmeSanitizeLog.md), [flash memory](FlashMemory.md)

## Introduction

Sanitize is an NVMe command guaranteeing that all user data, including data in those normally-inaccessible reserve areas, becomes unrecoverable. It's stronger than the older `format` command.

Deleting a file (or reformatting a drive) doesn't actually erase the data. A "delete" just frees up a logical address, but the physical bits often remain -- sometimes in areas like spare blocks or retired bad blocks that a normal format can't even reach.

## Basic usage

Sanitize is run on a controller, ex: `/dev/nvme0`.

```bash
# crypto erase (faster)
sudo nvme sanitize /dev/nvme0 --sanact=start-crypto-erase

# block erase (slower)
sudo nvme sanitize /dev/nvme0 --sanact=start-block-erase

# overwrite (writes a pattern, less commonly used)
sudo nvme sanitize /dev/nvme0 --sanact=start-overwrite

# sanitize commands happen asynchronously, so use this
# to monitor Sanitize Operation Status
sudo nvme sanitize-log /dev/nvme0 -H
```
