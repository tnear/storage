# Extent

An *extent* is a contiguous (unbroken) range of disk blocks that belong to a file.

See also: [Fragmentation](Fragmentation.md)

## Introduction

Instead of mapping and listing every individual block pointer for a file, an extent only requires a starting address and length, which cuts down on metadata overhead and improves read-write performance.

## Example

This is the file:
```
  0    1    2    3
[ A ][ A ][ A ][ A ]
```

Instead of recording four separate blocks, the filesystem can describe them as one extent:

```
start block: 0
length: 4 blocks
```

### Fragmented example

If the file is instead fragmented, it may look like this:

```
  0    1    2    3    4    5
[ A ][ A ][ X ][ X ][ A ][ A ]
```

This requires two extents for File A:
```
extent 1: blocks 0-1
extent 2: blocks 4-5
```
