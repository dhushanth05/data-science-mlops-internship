# Operating Systems: File System Concepts

## Introduction

A **file system** is a method used by an operating system to organize, store, manage, and access data on a storage device.

Whenever we create a file, save a document, open a folder, or delete data, the operating system uses the file system to manage that data.

This article covers the important general file-system concepts used in operating systems.

---

## 1. File

A **file** is a collection of data stored under a specific name.

Examples include:

- Text files
- Images
- Videos
- CSV files
- Programs
- Documents

The file system keeps track of where the file is stored and how it can be accessed.

---

## 2. Directory

A **directory**, commonly called a folder, is used to organize files and other directories.

For example:

```text
project/
├── data/
│   ├── customers.csv
│   └── sales.csv
└── scripts/
    └── analysis.py
```

Here, `project`, `data`, and `scripts` are directories.

---

## 3. File-System Hierarchy

A file system normally follows a hierarchical structure.

A directory can contain:

- Files
- Subdirectories

This creates a tree-like structure.

For example:

```text
/
├── home/
│   └── user/
│       ├── documents/
│       └── downloads/
├── etc/
└── var/
```

The top-level directory is commonly called the **root directory**.

---

## 4. File Path

A **file path** tells the operating system where a file or directory is located.

For example:

```text
/home/user/documents/report.txt
```

The path identifies the location of `report.txt` inside the directory hierarchy.

---

## 5. Absolute Path

An **absolute path** gives the complete location of a file starting from the root of the file system.

Example in Linux:

```text
/home/user/documents/report.txt
```

Example in Windows:

```text
C:\Users\User\Documents\report.txt
```

An absolute path does not depend on the current directory.

---

## 6. Relative Path

A **relative path** describes a file location relative to the current working directory.

For example, if the current directory is:

```text
/home/user/documents/
```

then:

```text
report.txt
```

is a relative path.

Relative paths are useful when working within a project or directory structure.

---

## 7. File Name and Extension

A file normally has a **name** and may have an **extension**.

Example:

```text
sales.csv
```

Here:

- `sales` → file name
- `.csv` → extension

The extension commonly indicates the type or format of the file.

Examples:

```text
report.txt
image.jpg
data.csv
program.py
```

The exact meaning and handling of extensions can depend on the operating system and application.

---

## 8. File Metadata

**Metadata** is information about a file rather than the actual contents of the file.

Common metadata includes:

- File name
- File size
- Creation time
- Modification time
- Access time
- File type
- Owner
- Permissions

For example, a 10 MB CSV file has its actual data as content, while its size, owner, and modification time are metadata.

---

## 9. File Attributes

**File attributes** are properties or flags associated with a file.

Depending on the operating system, attributes can indicate things such as:

- Read-only
- Hidden
- System file
- Archive status

Attributes help the operating system determine how a file should be treated.

---

## 10. File Permissions

**File permissions** control who can access or modify a file.

In Linux and Unix-like systems, permissions commonly include:

- `r` → read
- `w` → write
- `x` → execute

Permissions can be assigned to:

- Owner
- Group
- Others

For example:

```text
rwxr-xr--
```

This controls what different users are allowed to do with the file.

---

## 11. File Ownership

File ownership identifies which user and group are associated with a file.

For example, in Linux a file can have:

```text
Owner: user1
Group: developers
```

Ownership works together with permissions to control access to files.

---

## 12. Storage Blocks / Clusters

Storage devices are divided into smaller units used for storing data.

In different file systems these units may be referred to as **blocks**, **clusters**, or similar terms.

A large file is usually stored across multiple storage units.

For example:

```text
Large File
   ↓
Block 1
Block 2
Block 3
Block 4
```

The file system keeps track of these units so that the complete file can be accessed.

---

## 13. File Allocation

**File allocation** is the method used by a file system to decide where the blocks or storage units belonging to a file are placed.

Common allocation approaches include:

- Contiguous allocation
- Linked allocation
- Indexed allocation

Modern file systems use more advanced allocation techniques to improve performance and storage efficiency.

---

## 14. Fragmentation

**Fragmentation** occurs when the storage space used by files becomes divided into separate areas.

### Internal Fragmentation

Unused space can exist inside an allocated storage unit.

### External Fragmentation

Free space can become divided into many smaller areas.

Fragmentation can affect storage efficiency and, depending on the file system and storage device, performance.

---

## 15. File Operations

Common file operations include:

- Create
- Open
- Read
- Write
- Append
- Rename
- Delete
- Close

For example, when a program reads a CSV file, it first opens the file, reads the required data, and then closes the file.

---

## 16. Open and Close

Before a program can normally read from or write to a file, it opens the file.

When a file is opened, the operating system creates the necessary information for managing that open file.

After the required operations are completed, the program closes the file.

Basic flow:

```text
Open
  ↓
Read / Write
  ↓
Close
```

Closing files helps release operating-system resources.

---

## 17. File Descriptor / Handle

When a process opens a file, the operating system provides a reference that the process can use to access that open file.

On Unix/Linux systems, this reference is commonly called a **file descriptor**.

For example:

```text
0 → Standard Input
1 → Standard Output
2 → Standard Error
```

Other opened files can receive additional file descriptors.

On Windows, a similar concept is commonly represented using a **file handle**.

---

## 18. Buffering and Caching

**Buffering** temporarily holds data while it is being transferred between a program and storage.

**Caching** keeps frequently used data in faster memory so it can be accessed more quickly.

For example:

```text
Application
     ↓
Buffer / Cache
     ↓
File System
     ↓
Storage Device
```

These techniques can reduce the number of slow storage operations and improve performance.

---

## 19. File Locking

**File locking** is used to control simultaneous access to a file.

For example, if two processes try to modify the same file at the same time, their changes could conflict.

A lock can help control this access.

Common concepts include:

- Shared/read locks
- Exclusive/write locks

The exact locking behavior depends on the operating system and file system.

---

## 20. Mounting

**Mounting** makes a file system available at a particular location in the operating system's directory structure.

For example, in Linux, a storage device or partition can be mounted at:

```text
/mnt/data
```

After mounting, its files can be accessed through that directory.

Conceptually:

```text
Storage Device
      ↓
 File System
      ↓
 Mounted at /mnt/data
```

---

## 21. Different File Systems

Different operating systems and storage environments use different file systems.

### Windows

```text
NTFS
FAT32
exFAT
```

### Linux

```text
ext4
XFS
Btrfs
```

### Apple Systems

```text
APFS
```

They all solve the general problem of managing stored data, but they differ in features, structure, limits, permissions, performance, and other behavior.

---

## 22. Journaling

**Journaling** is a technique used by some file systems to improve reliability.

Before making certain changes to the main file-system structures, the file system can record information about the intended changes in a journal.

If the system crashes during an operation, the journal can help the file system recover to a consistent state.

Conceptually:

```text
Change Request
      ↓
   Journal
      ↓
File-System Update
```

Journaling helps reduce the risk of file-system corruption after unexpected shutdowns or crashes.

---

## 23. File System vs Storage Device

A **storage device** is the physical or logical hardware used to store data.

Examples:

- HDD
- SSD
- USB drive
- Memory card

A **file system** is the structure and set of rules used by the operating system to organize and manage data on that storage.

For example:

```text
SSD
 ↓
File System (NTFS / ext4 / APFS)
 ↓
Directories and Files
```

So, a storage device provides the storage capacity, while a file system organizes and manages the data stored on it.

---

# How These Concepts Work Together

When a user saves a file, many of these concepts work together.

For example:

```text
User creates report.csv
          ↓
Operating System
          ↓
File System
          ↓
Directory + File Name
          ↓
Metadata + Permissions + Ownership
          ↓
File Allocation
          ↓
Storage Blocks / Clusters
          ↓
Storage Device
```

When the file is opened later:

```text
Application
    ↓
Open File
    ↓
File Descriptor / Handle
    ↓
File System
    ↓
Storage Blocks
    ↓
Data returned to Application
```

Buffering, caching, permissions, locking, and other mechanisms may also be involved depending on the operation.

---

# Conclusion

A file system is one of the fundamental components of an operating system. It provides a structured way to store, organize, locate, protect, and access data.

Understanding concepts such as **files, directories, paths, metadata, permissions, ownership, storage blocks, allocation, fragmentation, file operations, file descriptors, caching, locking, mounting, journaling, and different file systems** provides a strong foundation for working with operating systems and data-engineering technologies.

These concepts are also important when moving from traditional operating-system file systems to distributed file systems such as **HDFS**, where data is managed across multiple machines.
