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

The **Block Allocation Map** tells us whether a block is free or occupied. If occupied, there is a type (found in `./define/constants.h`) of the block. It requires one byte per block, so the BAM requires **8192/2048 = 4 blocks** of space.

For a more detailed explanation, you can take a look at [NITCbase's explanation.](https://nitcbase.github.io/docs/Design/Physical%20Layer/#internal-index-block-structure)


