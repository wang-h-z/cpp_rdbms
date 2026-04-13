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


## Part 1: Understanding the Physical Layer





