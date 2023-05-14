> [!info] 
> <u>File</u>: linear of array of bytes, each of which you can read or write.
> 	- low level name assigned to file - **inode number**
> 	- content (type) of a file is not known to the OS.
> <u>Directory</u>: it contains a list of (user-readable name, low-level name) pairs.
> 	- It also has a inode number.

##### Open
- `int fd = open("foo", O_CREAT|O_WRONLY|O_TRUNC, S_IRUSR|S_IWUSR);`
	- 'foo' -> user-readable file name
	- O_CREAT -> create file, O_WRONLY -> allow write to file, O_TRUNC -> truncate file to 0 bytes (remove existing content)
	- S_IRUSR/IWUSR -> read,write  permissions to owner
	- fd -> file descriptor

> [!important] 
> **File Descriptor**
> - A file descriptor is just an integer, <u>private per process</u>, and is used in UNIX systems to allow access to files for the process, assigned by OS to identify each open file. 
> - When a process <u>opens</u> a file, the operating system assigns a file descriptor to the open file and returns the descriptor to the process. The process can then use the descriptor to read from or write to the file.
> - Each process has its own fd table which is array of structures with each structure storing info about fd, file's current position and other attributes.

> [!tip] 
> Already open file descriptors -> (0-input, 1-output, 2-error)
> 

##### Sequential Read/Write

![[read-write strace.png]]

1. Gets fd using open command (if it can access) in readonly with 64 bit offset
2. Read fd = 3, output of read stored in buffer and can be seen in 2nd arg, 3rd arg is size of buffer (4kb). Returns number of bytes to read.
3. cat uses write command with fd of std:output because it is more optimized but usually a process would call printf to figure out formatting details
4. Prints result to screen
5. Tries to read again but read returns 0, so it closes the file using fd.

![[sequential read-write.png]]

##### Random Read/Write

![[file_struct.png]]
- Struct file pointed to by fd
	- also stores current offset and file inode and permissions

![[lseek.png]]
- Lseek to read from input offset 

![[open file table.png]]
-  Open file table per process with single lock that may store only array of fds pointing to struct files or struct files (File Control Blocks) themselves along with the fd
- Linux has a system wide OFT

![[multiple open same file.png]]
-> Offset for each fd updates independently

![[lseek read.png]]
-> How lseek changes offset

##### Shared File Table Entries

1. fork()

![[fork reference count change.png]]

Parent and child process share same open file table and refcount of the fd increases.
Offset change by one reflects in the other

1. dup(), dup2(), dup3

The dup() call allows a process to create a new file descriptor that refers to the same underlying open file as an existing descriptor.
 - dup() command can be used to while redirecting standard input/output/error to another file by copying the same file descriptor
 - It can also be used to create backup while writing to a file.
 - It is also used in piping the output of one process to another.

##### Writing immediately with fsync
> [!info] 
> fsync (fd) can be used to write all dirty (not yet written) data to disk, for the file referred to by the specified file descriptor. 
- Used in DBMS, power loss in machine may result in incomplete write to disk.
- Sometimes you also need to fsync the directory for this to work as expected.

##### Renaming a File
`rename(char *old, char *new)`
- Atomic call
- Useful to create temp files and ensure everything has been written and then rename it to original file.

##### Getting Metadata of a file
`stat() or fstat()` - take path or file descriptor as argument
![[stat struct.png]]
- Each file system usually keeps this kind of structures in **inode**
- Inode is avlbl on disk as well as cached on memory for faster access

##### Remove File
> [!tip] 
> unlink() with filename
> 

#### Directories

**Making** - `mkdir("foo", 0777)`, also contains `.` (self) and `..` (parent)
**Opening** - `ls` 
![[dir struct.png]]
**Deleting** - `rmdir` (only deletes empty dirs)

##### Hard Links

> [!info] 
> The way link() works is that it simply creates another name in the directory you are creating the link to, and refers it to the same inode number (i.e., low-level name) of the original file. 

The file is not copied in any way; rather, you now just have two human-readable names (file and file2) that both refer to the same file.

![[hard links.png]]

> [!fail] 
> You can’t hard link to files in other disk partitions (because inode numbers are only unique within a particular file system, not across file systems) 

> [!important] 
> **How unlink works**
> - Removes link between human-readable file name and inode and decrements the reference count of inode.
> - Only when the ref count reaches 0, the fs will free the inode number and related data blocks (truly delete)

##### Symbolic Links

-> Soft links (<u>a file type in themselves</u>)

> [!info] 
> The way a symbolic link is formed is by holding the pathname of the linked-to file as the data of the link file. 

> [!caution] 
> **Dangling Reference** - quite unlike hard links, removing the original file named file causes the link to point to a pathname that no longer exists.

##### Permissions

The permissions consist of three groupings: what the owner of the file
can do to it, what someone in a group can do to the file, and finally, what
anyone (sometimes referred to as other) can do.

- include the ability to read the file, write it, or execute it.
- file mode (permissions) can be changed by changing the permission bits using `chmod`

> [!info] 
> - **For Directories** - enable a user (or group, or everyone) to do things like change directories (i.e., cd) into the given directory, and, in combination with the writable bit, create files in it
>- **For AFS** - include more sophisticated controls. AFS, for example, does this in the form of an <u>access control list (ACL)</u> per directory. 

Access control lists are a more general and powerful way to represent exactly who can access a given resource. In a file system, this enables a user to create a very specific list of who can and cannot read a set of files, in contrast to the somewhat limited
owner/group/everyone model of permissions bits described above.

#### Mount

> [!info] 
> Instead of having a number of separate file systems, mount unifies all file systems
into one tree, making naming uniform and convenient. 

> [!info] 
> To make a file system, most file systems provide a tool, usually referred to as **mkfs** (pronounced “make fs”), that performs exactly this task. 

The idea is as follows: give the tool, as input, a device (such as a disk partition, e.g., /dev/sda1) and a file system type (e.g., ext3), and it simply writes an empty file system, starting with a root directory, onto that disk partition.