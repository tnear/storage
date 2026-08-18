# Fragmentation

Hard drive fragmentation is when the pieces of a file are stored in non-contiguous locations on a disk instead of being laid out together.

On a spinning hard drive, that matters because the read/write head must physically move around to collect the pieces.

See also: [Extent](Extent.md)

## Introduction

A hard drive stores a file in small pieces called *blocks*. Ideally, the blocks of one file are adjacent, ex: `[ A ][ A ][ A ]`.

When the blocks for a file are split up, it is *fragmented*. If other data, `X`, appears between `A`'s blocks: `[ A ][ A ][ X ][ A ]`.

## HDD challenges

Fragmentation slows spinning hard drives. They have a read head which must move a longer distance to read all the blocks of one file. For the example above, the read head must:

```
read A
move past X
read more A
```

## Why fragmentation happens

If a disk initially has this:

```
| File A | File B | File C |
```

But then B is deleted:

```
| File A | free   | File C |
```

Next, File A grows, but the `free` space is too small. The filesystem may put the new portion elsewhere:

```
| File A (part 1) | other data | File A (part 2) |
```

File A is now fragmented, and over time can become worse without periodic defragmentation.

### Defragmentation

Defragmentation rearranges file blocks so they are more contiguous.

```
Before:
AAA .... AA .... A

After:
AAAAAA
```

On Windows this is done by a defragmenter. On Linux, there are filesystem-specific tools (Ex: `e4defrag` for ext4.

## Why SSDs are different

Fragmentation is much less important for SSDs because they have no mechanical head. Random access for SSDs is cheaper because there is no read head which must physically move.

SSDs generally should not be defragmentated for two reasons:
1. Faster random access
1. Health concerns about program/erase cycles
