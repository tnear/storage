# SEDutil

`sedutil-cli` - util to manage TCG Opal 2.0 self encrypting drives

## Basic commands

```bash
# print opal compliant disks
sedutil-cli --scan

# show details info about a specific drive
sudo sedutil-cli --query /dev/nvme0

# password
sudo sedutil-cli --printDefaultPassword /dev/nvme0
MSID: <32_char_string>

# take ownership plus activate Locking SP
$ sudo sedutil-cli --initialSetup "$PIN" /dev/nvme0
takeOwnership complete
Locking SP Activate Complete
...

$ PIN='test-pin'  # should use more secure PIN in production
$ sudo sedutil-cli -v --listLockingRanges "$PIN" /dev/nvme0
LR0 Begin 0 for 0
            RLKEna = N  WLKEna = N  RLocked = N  WLocked = N
# LR0: global range (entire drive)
# Begin 0 for 0: global range
# RLKEna = N, WLKEna = N: read/write locking enforcement not enabled yet
# RLocked = N, WLocked = N: it is currently unlocked, but since locking
#    is not enabled, that is not meaningful yet.

# Enable global range lock using $PIN as the Admin1 password.
# This does *not* lock the drive.
$ sudo sedutil-cli -v --enableLockingRange 0 "$PIN" /dev/nvme0
Enabling Locking Range 0
LockingRange0 enabled ReadLocking,WriteLocking

# Read and Write locking are both now enabled
$ sudo sedutil-cli -v --listLockingRanges "$PIN" /dev/nvme0
LR0 Begin 0 for 0
            RLKEna = Y  WLKEna = Y  RLocked = N  WLocked = N

# Lock global range
sudo sedutil-cli -v --setLockingRange 0 LK "$PIN" /dev/nvme0

# Query global range lock (RLocked and WLocked are Y)
$ sudo sedutil-cli -v --listLockingRanges "$PIN" /dev/nvme0
LR0 Begin 0 for 0
            RLKEna = Y  WLKEna = Y  RLocked = Y  WLocked = Y

# Unlock global range
sudo sedutil-cli -v --setLockingRange 0 RW "$PIN" /dev/nvme0

# Verify RLocked and WLocked are back to N
sudo sedutil-cli -v --listLockingRanges "$PIN" /dev/nvme0
LR0 Begin 0 for 0
            RLKEna = Y  WLKEna = Y  RLocked = N  WLocked = N

# Here I did reboot testing. This swapped nvme0 with nvme1.
# Must verify serial number.

# Try wrong pin while locked
sudo sedutil-cli -v --setLockingRange 0 LK "$PIN" "$DRIVE"  # lock
sudo sedutil-cli -v --setLockingRange 0 RW wrong-pin "$DRIVE"
method status code NOT_AUTHORIZED
Session start failed rc = 1
One or more header fields have 0 length
EndSession Failed

# Unlock global range (again)
sudo sedutil-cli -v --setLockingRange 0 RW "$PIN" "$DRIVE"

# Idempotency test
sudo sedutil-cli -v --setLockingRange 0 RW "$PIN" "$DRIVE"
sudo sedutil-cli -v --setLockingRange 0 RW "$PIN" "$DRIVE"
sudo sedutil-cli -v --enableLockingRange 0 "$PIN" "$DRIVE"
sudo sedutil-cli -v --listLockingRanges "$PIN" "$DRIVE"

# Reset state using PSID
$ sudo sedutil-cli -v --yesIreallywanttoERASEALLmydatausingthePSID "$PSID" "$DRIVE"
revertTper completed successfully

# Verify old PIN no longer works
sudo sedutil-cli -v --listLockingRanges "$PIN" "$DRIVE"
method status code INVALID_PARAMETER
Session start failed rc = 12
One or more header fields have 0 length
EndSession Failed
```

## Resources
- https://sedutil.com/
