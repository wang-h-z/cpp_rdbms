# C++ RDBMS

This project is an ongoing Relational Database Management System (RDBMS) implementation project with the help of [NITCbase](https://nitcbase.github.io/) roadmap as an implementation guide.

## Part 0: Understanding the project structure

The project structure is as follows: 

```cpp
  NITCbase
  .
  ├── Disk
  ├── XFS_Interface
  ├── Files
  │   ├── Batch_Execution_Files
  │   ├── Input_Files
  │   └── Output_Files
  └── myrdbms
      ├── define
      ├── Disk_Class
      ├── Buffer
      ├── Cache
      ├── BPlusTree
      ├── Schema
      ├── Algebra
      ├── Frontend
      ├── FrontendInterface
      .
      .
      .
```

Important directories/files to note for now are: 

* `Disk/`: contains the `disk` binary file where storage is simulated
* `XFS_Interface/`: executable `xfs_interface` is present here

> [!NOTE]
> What is a XFS_Interface?
>
> XFS (eXternal File System) interface is a utility tool that sits outside the RDBMS and gives direct access to the disk. 
>
> The XFS lets you: 
>
> `DUMP BMAP` - print the Block Allocation Map 
>
> `PRINT TABLE` - view table contents directly from disk
>
> `PRINT B+ TREE` - visualize index structure

## Running the XFS Interface

1. Go to the `XFS_Interface/` folder and run these commands:

```c++
./xfs-interface
```

2. You should now be in inside the XFS interface prompt. Run the fdisk command to create the disk.xfs file

```c++
# fdisk
```

3. You should see:

```c++
# fdisk
Disk formatted
#
```

4. Now that we have the disk file we can exit:

```c++
# exit
```

## Part 1: Understanding the Physical Layer

Here are the essential details to understand this project's physical layer.

## Disk Model
The disk consists of **8192 blocks**, and each block is **2048 bytes**. 

The **Block Allocation Map** tells us whether a block is free or occupied. If occupied, there is a type (found in [`constants.h`](NITCbase/myrdbms/define/constants.h)) of the block. It requires one byte per block, so the BAM requires **8192/2048 = 4 blocks** of space.

## Disk Class

Higher layers access the disk using the disk class.

The disk class has 2 main functions `readBlock()` and `writeBlock()`.

## Blocks and Block Types

This project uses three types of block types:

1. [Record Block](#record-block)
2. [Internal Index Block](#internal-index-block)
3. [Leaf Index Block](#leaf-index-block)

### Record Block

Records in a relation are composed of fields known as *attributes*. This projet fixes the size of the attributes as 16 bytes, but the records of a relation can be of variable size.

A record block stores the actual data records. There is also some additional metadata stored in the header of each block.

### Internal Index Block

This project uses the B+ tree data structure for indexing records in a relation.The DMBS may maintain zero or more indices per relation. 

In a B+ tree, there are both internal nodes and also leaf nodes. A disk block that stores an internal node is an **Internal Index Block**. 

Each interanal index block stores 100 atributes (keys) values. Each of these keys has an associated left and right child pointers. 

### Leaf Index Block

A lead node in the B+ tree stores the actual indices for the records in a relation. In this project, an index is a 3-tuple: `(attribute value, block number, slot number)`. The size of an index is 32 bytes (last 8 bytes unused)
.

For a more detailed explanation, you can take a look at [NITCbase's explanation.](https://nitcbase.github.io/docs/Design/Physical%20Layer/#internal-index-block-structure)


## Reading and Writing from using the Disk class

In `main.cpp`, we can write some simple code to read and write a string to the `Disk` using the Disk class.

```cpp
int main(int argc, char *argv[]) {
  Disk disk_run;

  unsigned char buffer[BLOCK_SIZE];
  Disk::readBlock(buffer, 7000); //random index of 7000
  char message[] = "hello";
  memcpy(buffer + 20, message, 6);
  Disk::writeBlock(buffer, 7000);

  unsigned char buffer2[BLOCK_SIZE];
  char message2[6];
  Disk::readBlock(buffer2, 7000);
  memcpy(message2, buffer2 + 20, 6);
    std::cout << message2 << std::endl;

  return 0;
}
```

```terminal
In your terminal run: 

cd mynitcbase
make
./nitcbase
```

You should see:

```
hello
```

You can also read the **Block Allocation Map** like so: 

```cpp
// Read all 4 BAM blocks
  for (int i = 0; i < BLOCK_ALLOCATION_MAP_SIZE; i++) {
      unsigned char buffer[BLOCK_SIZE];
      Disk::readBlock(buffer, i);

      // Each byte in this block represents one disk block's type
      for (int j = 0; j < BLOCK_SIZE; j++) {
          int blockNum = i * BLOCK_SIZE + j;
          std::cout << "Byte " << blockNum << " type: " << (int)buffer[j] << "\n";
      }
```

We will notice that the first 4 values are:

4, 4, 4, 4

And then followed by two 0s (for the catalogs) and then the rest of the values are 3.

These values correspond to the BMAP, REC and UNUSED_BLK constants.

## Part 2: Record Blocks and Catalogs