---
cssclass:img-grid
---


# Intro to Data Management

We have a continuous flow of data coming out of a data source (DAQ) at a very high rate.
We want to process it now and then (usually for hours) to check the data and prepare it.
Then we want to reprocess it every once in a while (~year) to incorporate the knowledge gathered.
Then after all is said and done you put everything in cold storage (long-term storage).

This is the usual path a datasets take to be born.

But what is a **Dataset**?

```ad-definition
title: Dataset

A set of information collected by several sources throughout a stretch of time associated with a unique field or purpose

```

We refer to every single *datum* ("piece of data") as a **record**.

But how to store information about the datasets? How do store store the **metadata**? 

The metadata is the information about the datasets and it's usually "stored" as the underlying structure of our datasets.
There are 3 types of metadata:
- *Descriptive*
- *Structural*
- *Administrative*

Based on how a dataset is constructed we have 3 large types of datasets:
- *Structured*: the data schema is known and fixed
- *Semi-structured*: the data schema is not necessarily known or fixed apriori, but it is inferred from the data
- *Unstructured*: no data schema is available a-priori, nor can be inferred from the data


<div style="page-break-after: always;"></div>



# Data Storage
Different necessities call for different data-storage solutions.

We can need storage that needs to withstand large throughput, can do a huge number of operations and therefore can only store a small amount of data (the processed data is moved quickly away).
This is a very "hot" storage.

Then we have storage that needs to have a large amount of data stored and can perform an average number of operations.

Then we have "cold" storage, where we write once, read once every few years, and have a *very large* amount of data stored.

##### Storage Concepts
Let's define some lingo:

- **IO**: single request for Input/Output operation
- **Latency**: time taken to complete a single IO request
- **[IO] Rate**: number of IO that can be performed by storage in a second of a given workload IO. "IO operations per second" => IOPS
- **Block Size**: Average size (in bytes) of an IO request of a given workload
- **Throughput**: data transfer rate to and from the storage media in megabytes per second [IO Block Size] x [IOPS]
##### Storage Media
###### DRAM

- Semiconductor memory technology (NAND cells storing 1 bit of data)
- Data is not persisted, only temporary storage cells (capacitors and transistors)
- Extremely low latency (0.1 $\mu s$)

###### Non-volatile Memory
- Several different technologies
- Hold data even if the device is turned off
- Higher storage capacity than DRAM
- Very low latency (1 μs)
- ==Extremely specialized solution for storage, rarely used in standard applications==

###### Solid State Drives (SSDs)
- Mostly based on NAND flash chipset for data storage and a controller for caching, error handling, etc
- Commonly Multi-Level charge trap instead of a single-value NAND cell
- No mechanical components (as in DRAMs and NVMs)
- Optimised performance implementations available (such as NVM Express SSDs)
- Relatively short latency (10-100 μs)
- Fast READ but slower WRITE IO    
- Limited number of WRITE per block over an SSD lifetime
- Useful for large-storage low-latency applications
###### Magnetic (Hard) Disk Drives (HDDs)
- A number of (double-sided) spinning platters covered in magnetic media
- Every bit of information is actually a flip in the magnetic field across two domains
- Data is stored in contiguous sectors (typically ~0.5 to 4 bytes) of the platter tracks
- IO requests are managed by a moving arm assembly placing its heads on the appropriate track to READ/WRITE data from sectors
- Several latency sources (10 ms):
	1. Time to move the head to the proper track (seek time)
	2. Time for the disk to rotate and align to the appropriate sector (rotation delay)
###### Magnetic Tapes
- Magnetic tape rolled up in cartridges
- Usually managed by robotic arms to move/swap cartridges
- Data (e.g. entire files) are stored sequentially (contiguous) over the tape
- From an IO request to the actual operation to be complete several steps occurs:
    1. Robotic arms fetch the cartridge
    2. Unroll the tape to the point where the data
    3. Read sequentially all memory locations
- While the actual READ IO is fast (!) the overall latency is of the order of 100 s (or more)
- Used for write-once operations and long-term safekeeping

###### Striking a Balance
![[Screenshot 2024-02-29 at 09.04.01.png]]

One important thing is **Kryder's Law**:
"The storage density doubles every 13 months"
![[Screenshot 2024-02-29 at 09.06.02.png]]

##### Scalability
Most datasets are expected to grow over time so the question is:
- [?] How to handle an increase in the size of the dataset maintaining or even optimizing the performance?

Let's assume that we start from here: a system with a given storage solution. *How do we scale this solution for a larger dataset?*

This graph sum it up:
![[Screenshot 2024-02-29 at 09.12.19.png]]
###### Vertical
*Pros*:
- No real changes in how user acces the resources
- Fast access time to the HW resources

*Cons*
- The larger the storage solution, the more expensive it gets
- There's an upper limit to the solution one could get at a given time
- Stop operations -> Upgrade -> Resume
###### Horizontal
*Pros*
- Can grow to very large systems for cheaper
- Easy to scale as you go 

*Cons*
- Multiple HW resources "connected" together: network bandwidth is a very important and limiting factor
- More complex solution to access all HW resources: different from local systems


```ad-summary
title: Your Storage

In Unix system command `dd` and `fio`

```

```ad-exercise

Based on throughput guess the type of storage media

```

<div style="page-break-after: always;"></div>

# Reliability and Security
## Reliability
Preserving stored data is extremely important, and we must keep it safe from *Data Loss*, *Data Corruption* and *Errors*.

**Data Loss** is due to storage failure, like mechanical, electrical, etc failure of the hardware. All data from the hardware damaged is lost.

**Data Corruption** can be due to single-bit errors over several read-write IO. Usually, 1 file is corrupted every 10000 files written.

**Errors** can be from software or transfer eg: not performing consistency checks when copying or transferring data.

```ad-definition
title: Reliability

Reliability is defined as the probability that a system will continue to perform correctly after a given time/ number of operations

```

Reliability can be measured by the *Mean Time To Failure MTTF* or *Mean Time Between Failure MTBF*

```ad-example
A realistic MTTF of a single HDD is ~ 10 years.
Assuming a flat prior on the probability of a single HDD to incur in a failure in any given day, we expect:
$$p = 1/(10 \text{years} * 365) = 0.00027$$

What could this translate if we had N = 5000 HDDs? How many HDDs would we expect to fail per day?

Assuming all disks are independent, according to the binomial distribution:
$$\mathbb{E}[x] \approx Np = 5000 \times 0.00027 = 1.35$$

At CERN with 100000 HDDs, this would translate to 27 expected HDD failing every single day.
```

##### Strategies
- **Mirroring**: Replicate data across multiple storage elements
- **Striping**: subdivide data into pieces and scatter it across multiple storage elements
- **Checksum/Parity Checks**: Ensure data consistency by detecting (and possibly correcting) errors

##### RAID
Improving storage reliability with *disk redundancy*.

**RAID**: Redundant Array of Independent Disks

Multiple physical devices are combined into a single *logical* one to improve reliability and/or performance.
Can be implemented is SW but mostly using a HW controller board to daisy-chain multiple disks.
There are a number of RAID schemas: RAID 0, RAID 1, RAID 1+0, RAID 5, RAID 6 ...

###### RAID 0
There is disk striping but no mirroring.
- Use 2+ disks to store the data
- Data is striped across all disks
- No data mirroring
When combining 2 disks the overall storage capacity will be 2x the capacity of the smallest one.
Used mostly for performance rather than reliability: can read/write at the same time for all disks.
But **losing 1 disk will mean losing ALL data!!**

###### RAID 1
Mirroring but no striping
- Use 2+ disks to store the data
- Data is mirrored across all the disks
- No data striping
When combining 2 disks, the overall storage capacity will be 1x the capacity of the smallest one.

Used for reliability but is usually bad for performance. Write at the slowest disk throughput.

But **data is preserved as long as at least 1 disk is functional**.

###### RAID 1+0 (or RAID 10)
- Use 4+ disk to store the data
- Data is mirrored (RAID 1) across 2+ disks
- Data is striped (RAID 0) across 2+ RAID 1 disks

Good overall performance (close to RAID 0) but improved reliability (from RAID 1 mirroring):
- Read is fast due to striping
- Write is slower due to mirroring
But **data is preserved as long as at least 1 disk is functional in each mirrored pair**.




###### RAID with Parity
A *parity* information can be added to data blocks as an error protection scheme, and is usually based on XOR logic.

This approach means that in case of a **single** disk failure, data **can** be recovered from a single bit parity information.
In case of *multiple* failures data **cannot** be recovered.

###### RAID 5
Only striping + parity
-  Use 2+ disks to store the data +1 to store parity
- Data is striped across all disks
- No data mirroring
- Block parity is distributed across all disks so all disks can participate in satisfying read requests
Similar to RAID 0 (with $n-1$ disks), with good overall performances.
**Up to 1 disk loss, up to 1 block error recovery**.

###### RAID 6
Only striping + double parity

- Use 2+ disks to store the data +2 to store the parity
- Data is striped across all the disks
- No data mirroring
- Two parity infos are distributed across all disks
Extends over the RAID 5 to enable data recovery even in case of double error or 2 disk loss.

The 2 independent parity information are typically evaluated by error-correcting codes such as the Reed-Solomon codes.

**Up to 2 disk losses, up to 2 block errors recovery**.



## Security
Some important aspects of security are:
- **Confidentiality**: Ensure that nobody can extract knowledge of what you transfer, even if listening to the whole conversation
- **Integrity**: Ensure that the message has not been modified during the transmission
- **Authenticity, Identity, Non-Repudiation**: You can verify that you are talking to, the specific individual you are talking to, and the individual cannot deny being there

*Authentication* is to identify users and validate their identity.
*Authorization* is granting a user permission to access/use specific resources

##### Encryption
A simple example is passing a message $M$ of length $L$ between two users, both users also exchange a secret key $K$ of the same length $L$ to scramble and reassemble the data. A simple XOR logic function can be used to encode-decode the information.

###### Symmetric Key Encryption
Probably the simple encryption tecnique. Algorithms includes AES-128, AES-192, AES-256. They are simple so are very fast and very simple to implement.
However, all ends of the data communication have to exchange the same private key used to encrypt the data to be able to decrypt it.
For this reason, it is mostly used as a "2nd layer" of encryption, after a more robust first stage

###### Asymmetric Key Encryption
Each user has a key pair: public (to encrypt) and private (to decrypt).
The relation between the two keys is unknown and from one key is not possible to infer the other.
Algorithms include: RSA, ECC... 
Typically substantially slower than symmetric ones, but much more secure (standard for connecting to remote services).

##### Hash Functions
Hashing refers to the scrambling of raw information to the extent that it cannot be reproduced back to its original form.

Hashing algorithms (MD5, SHA-1, SHA-2,...) transform data of any size into a fixed-size short version of it, a **digest**.
Any small difference in the original data will result in a different digest.

Hashing algorithms area designed to avoid producing 2 identical digests for 2 different raw data: this concept is known as **hash collision**.

Hashing algorithms are used to:
- Mask/protect data
- Data integrity verifications
- Digital signature verifications

#### Encryption vs Hashing

Encryptions algorithms *encode and compress* data for confidentiality and to secure transfer.
They must be fully reversible to allow decryption, and can be computationally expensive.

Hashing algorithms scramble data into *fixed-sized* digests to hide/check data
They must not be reversible and must produce a completely different digest for any small difference in data (even 1 bit!). Must be computationally efficient and avoid **hash collision**.





<div style="page-break-after: always;"></div>

# File Systems
There are 3 main solutions to store/manage our datasets:
- **Files**: blocks of data stored in volumes managed by a File System $\implies$ Extension to Distributed FS
- **Databases**: data stored and managed via dedicated systems $\implies$ Extension to distributed DB (mostly NoSQL)
- **ObjectStores**: arbitrary data stored in "buckets" $\implies$ extensively used in Cloud Storage

The FS manages and tracks the location of data in terms of a *file hierarchy*.

- Data is **physically stored in blocks** scattered within the available storage devices
- The **abstraction** provided by the FS allows to identify data as files/directories by their name, path, and metadata
- The FS also provides the Operating Systems (OS) the interface for **low-level operations** with the storage blocks as files/directories: the common standard interface across most OSs is `POSIX`

Depending on the FS, files can be accessed and disk space can be allocated in several ways, including:
**File Access**:
1. Sequential Access
2. Direct/Random Access
3. Indexed Access
**Space Allocation**
1. Contiguous Allocation
2. Linked Allocation
3. Indexed Allocation


In an indexed FS, each file is described by its block, which in turn stores the indices ("pointers) to all other blocks containing the file's raw data

```ad-example
title: File System Example

Let's assume we have an indexed FS with 4kB blocks, each indirect pointer has an index of 4B. In total, the FS has 12 direct blocks and 1 single indirect block.
> [!question] How much data can we store using this FS?


- Each direct data block contains 4kB: $12 \times 4kB = 48 kB$
- Each single-indirect data block contains 4kB/4Bytes pointers
	- Each pointer points to a 4 kB data block
	- Thus each single-indirect data block describes $1000\times 4kB = 4MB$

This very old File System would be finally capable of storing 4048 kB.

> [!question] What if there was also 1 extra double-indirect block available?

- Each double-indirect data block contains 1000 pointers to indirect data blocks further containing 1000 pointers each
- Thus $1000 \times 1000 \times 4kB = 4GB$

So, given the block and index-sized of this particular example, this File System would be finally capable of storing:

$$n_{\text{direct}} \times 4kB + n_{\text{single-indirect}}\times {4}MB + n_{\text{double-indirect}} \times 4GB$$

```

Common File Systems sizes:

|          | Individual File Size Limit | Single volume size limit | Filename limit        | Metadata File Owner | Metadata  <br>File Permissions |
| -------- | -------------------------- | ------------------------ | --------------------- | ------------------- | ------------------------------ |
| *FAT32*  | 4 GiB                      | 16 TiB                   | 255 UCS-2 characters  | No                  | No                             |
| *NTFS*   | 16 EiB                     | 16 EiB                   | 255 characters        | Yes                 | Yes                            |
| *exFAT*  | 16 EiB                     | 64 ZiB                   | 255 UTF-16 characters | No                  | No                             |
| *HFS*    | 2 GiB                      | 2 TiB                    | 31 bytes              | No                  | No                             |
| *HFS+*   | 8 EiB                      | 8 EiB                    | 255 UTF-16 characters | Yes                 | Yes                            |
| *EXT2,3* | 16 GiB to 2 TiB            | 32 TiB                   | 255 bytes             | Yes                 | Yes                            |
| *EXT4*   | 16 GiB to 16 TiB           | 1 EiB                    | 255 bytes             | Yes                 | Yes                            |


So to summarise File Systems:

<mark style="background: #BBFABBA6;">Pros</mark>
- Can manage huge amounts of data (often EB)
- `POSIX` operations are FAST!
- Offers a hierarchical view of all data
- Single entities (files) can be easily transferred-copied-shared-processed

<mark style="background: #FF5582A6;">Cons</mark>
- Complex to define, maintain and scale a good data hierarchy for large datasets
- The files are "atomic entity", no underlying structure is accessible
- No way to group-select-filter the data based on file content

## From local to distributed

Distributed FS are ideally similar to local FS, but are extended to environments where both data and processing may be spread out across several nodes.

![[Screenshot 2024-03-06 at 09.15.33.png]]

The DFS resides on multiple nodes but offers a coherent and uniform view of data stored on all disks.

Several DFS are available, with very different implementations and applications:

- *NFS*: Network File System (1984)
- *AFS*: Andrew File System (1986)
- *Lustre*: Widely used in HighPerformanceComputeing (HPC) (2003)
- *EOS*: Developed at CERN (2010)
- *GFS*: Google File System (2003)
- *HDFS*: Hadoop Distributed File System (2006)

We have some *ideal* DFS requirements:

- **Transparency**: users should access the system regardless of where they log in from, be able to perform the same operations on DFSs and local FS, and should not be able to experience potential failures of part of the system
- **Fault Tolerance**: the distributed system should not halt in case of failures (even temporary) of parts of this infrastructure
- **Scalability**: the system should be efficiently leveraged from the usage of large numbers of servers (potentially dynamically) added into the system

```ad-example
title: DFS example: HDFS

DFS designed to run on commodity hardware for large data applications.
There are 2 main types of nodes:
- Namenode: nod hosting the metadata and taking all decisions
- Datanodes: several nodes hosting the data in blocks

A typical block size in HDFS is 128MB. Communication across nodes is based on TCP/IP.

The client requests are directed only to the namenode and it will reply and redirect the client to the appropriate block locations.

The typical flow of a `write`request is:

1. The client issues the request to the namenode
2. The namenode checks for authentication/access privileges/etc and provides the location where to write the data blocks
3. The client starts writing the data blocks directly to the datanodes w/o passing through the namenode
4. The data nodes will report back to the client to acknowledge the written completion

So *HDFS* provides **Transparency** and **Scalability**. But what about **Fault Tolerance**?
The main issues are:
1. Network Instability: the data nodes repeatedly send a "heartbeat" signal to the namenode
2. Datanode Failure: datablocks are replicated across multiple nodes (default 3). No need for RAID
3. Namenode Failure: **huge issue**, as it's a Single Point of Failure (SPOF). HDFS implements a secondary namenode to rebuild the metadata if the primary fails

```


### Cap Theorem

In computer science, three concepts are crucial when dealing with distributed systems.

- **Consistency**: Every request made to any data node returns the very same copy of data (the most recent version)
![[Screenshot 2024-03-06 at 09.29.15.png]]

- **Availability**: Every request to access/write/update data must receive a response even if one or more nodes are down
![[Screenshot 2024-03-06 at 09.30.53.png]]
- **Partition Tolerance**: The system continues to operate despite failures in the communication between nodes (known as *network partitions*)
![[Screenshot 2024-03-06 at 09.32.01.png]]


In computer science, the **CAP Theorem** (also Brewer's theorem) states that is impossible for a distributed system to simultaneously provide more than two out of the three C/A/P guarantees.
 In distributed systems there is no way to completely avoid Network Partitions, so to keep the cluster operational we must choose between Consistency and Availability.

- **A/P**: Get access to the data at any time, but no guarantee that the data will be the most recent value for each read
- **C/P**: Get the most recent value of data at any reads, but no guarantee that the system is available even in case of a network partition
- **A/C**: Both consistent and available, but cannot deliver fault tolerance in the case of a partition

> A/C is not an option in a distributed data management system BUT a common choice for vertically scalable systems

<div style="page-break-after: always;"></div>

# Relational Databases

A *database* can be loosely defined as a set of *records* organized and managed by a dedicated *management system*.![[Screenshot 2024-04-09 at 10.13.07.png]]
The database management system (DBMS) is a software that provides several basic functionalities to operate on the DB, including:
- Defining/creating data
- Querying/selecting data
- Manipulating / processing data

DBs tend to improve the management of large datasets over (D)FSs:
- DBs work with the granularity of individual records: (e.g. accessing single events of a dataset instead of all records in a file) ⇒ optimization over file-based operations 
- ALL data (and metadata) of a dataset is managed by the DBMS (e.g. data and detector configuration/banking wire transfers and users’ data) ⇒ all dataset information is accessible, including relationships between records
    
- Little-to-no replication is required to handle multiple “views” of the same data (e.g. store the whole dataset in a DB, and filter based on relations across records) ⇒ allows creating multiple subsets of the dataset without data replication

In DBs it’s often referred to the difference between the data “description” versus “content”:
- The Database **model** (or schema) is the *description* of the data and the relationship across various parts of the dataset
- The Database **Instance** (or state) is the *actual data* contained in the database at a given moment in time

Recalling the various data models, it’s clear that not all kinds of data can be described according to a simple formal logical schema: it’s easy for structured data and not so much for semi- or unstructured data

The DBMS is in charge of *defining the schema and updating/manipulating the instance of DB*.

Several DBs can be defined based on various conditions:
- Data Model
- Type of User access
- Architecture


The most relevant categorization from the users’ perspective is the one based on the data model (the way each record is represented).

## Database Models
In simple terms, each DB record can be thought of as a given data value indexed by a **key**, used as a unique data identifier.
The data itself might be structured, semi-structured, or unstrucutred, depending on the application.

![[Screenshot 2024-04-10 at 08.50.14.png]]

### Relational DB
Extremely common DB choice behind most real-life services and applications.
Ideal to have the mapping of relationships across multiple records of the dataset.
Based on relational algebra, a Relational DB requires a well-known and predefined schema, so *structured datasets only*.
RDBMS organizes the data in tables, allowing for the **mapping of relations across records of different tables**.


A primary key is an attribute or a combination of multiple attributes that identifies univocally a record.
- PKs must be *unique*
- PKs must be *minimal*

When assessing relationships across multiple tables, we refer to the primary key and the foreign key(s). These foreign keys are attributes acting as the link to the key to other tables.

Each set of operations on a Relational DB is referred to as a **Transaction**.
Transactions are **ACID** meaning that are operations characterized by:
- *Atomicity*: Either all operations in a transaction are executed in full or aborted (all-or-nothing)
- *Consistency*: Transactions must always move the DB from one valid state to another, preserving all the DB constraints
- *Isolation*: Every transaction must generate the same results as in a single-user environment
- *Durability*: After a transaction, the DB state persists until it is changed by another transaction

## SQL
The “de facto” standard for transactions on RDBs is **SQL (Structured Query Language)**.
SQL is a declarative language which contains the features of:
- *Data Definition Language*: to define schemas
- *Data Manipulation Language*: to query and modify data
- *Data Control Language*: to perform authorization and access control


To create a table a schema is required, all the attributes must be specified beforehand

```sql
CREATE TABLE Users(
	UserID varchar(30),
	BadgeNum int(16),
	FirstName varchar(255),
	LastName varchar(255),
	Age int,
	OtherAttr float
);
```

Upon creation of a table, the instance (the actual data contained) is empty.

The primary key can be defined as one attribute or a combination of multiple attributes with the “`PRIMARY KEY`” keyword.

Additionally, several constraints can be assigned to the attributes:
- `UNIQUE`
- `NOT NULL`
- `DEFAULT`
- `CHECK`

Once created the schema of a table can be modified, keeping in mind all the constraints previously defined.

```sql
ALTER TABLE Users
ADD COLUMN Role CHAR(1) DEFAULT 'A' CHECK (Role IN ('A', 'B', 'C'));
```

The alteration of the schema is going to be propagated to the entire table, all the records are going to be affected and it might take a long time.

In the case a new `NOT NULL` attribute is added, all records must now contain a valid value, hence a `DEFAULT` must be specified.
This is one of the many issues related to the maintenance of a RDB. The process of defining and maintaining the schema of an RDB is referred to as **Database Normalization**.

### SQL (DML)
SQL offers all CRUD operations: Create ←→ INSERT, Read ←→ SELECT, Update ←→ UPDATE, Delete ←→ DELETE


#### INSERT
```sql
INSERT INTO Users (UserID, BadgeNum, FirstName, LastName, OtherAttr)
VALUES ('user:0001',100,'Jacopo','Pazzini', 1.8)
```

#### UPDATE
```sql
UPDATE Users
SET Role = 'C'
WHERE (LastName = 'Pazzini')
```

#### DELETE
```sql
DELETE FROM Users
WHERE (FirstName LIKE '__c%')
```

#### SELECT
```sql
SELECT FirstName, OtherAttr*2, POWER(BadgeNum,2)
FROM Users
WHERE (OtherAttr > 3);
```

##### Aggregators
Aggregating functions are available:
`MIN, MAX, COUNT, AVF, SUM`

#### GROUP BY
```sql
SELECT COUNT(Country) AS nCountry, Country
FROM customers
GROUP BY Country
HAVING nCountry > 5
ORDER BY Country;
```

#### JOINS
The DB Schema will reflect relationships across multiple tables, which can be retrieved with `JOIN` operations on the keys. Joins are a central feature for mapping relationships and can be used to perform a number of relational operations.

![[Screenshot 2024-04-10 at 09.13.03.png]]
![[Screenshot 2024-04-10 at 09.12.56.png]]
![[Screenshot 2024-04-10 at 09.12.50.png]]
![[Screenshot 2024-04-10 at 09.12.43.png]]

#### VIEWS
```sql
CREATE VIEW IntalianCustomers AS
SELECT *
FROM Customers
WHERE Customers.Country = 'Italy';
```


## SQL (DCL)

The DCL component of the SQL language is extremely powerful, as it allows to:
- Create/remove users
- Assign/update passwords
`CREATE USER 'my_new_user'@'localhost' IDENTIFIED BY 'user_new_password'`
- Assign/update/remove privileges to users
`GRANT SELECT, UPDATE ON db_name.table_name TO ‘my_new_user’@’localhost’`
`REVOKE UPDATE ON db_name.table_name TO ‘my_new_user’@’localhost’`
- Create/maintain/roll-back DB transitions

<div style="page-break-after: always;"></div>

# NoSQL Databases

How DBs can be distributed across multiple nodes? *Partitioning and Sharding*.

**Partitioning**: subdividing the DB into multiple smaller parts

![[Screenshot 2024-04-10 at 09.18.44.png]]
![[Screenshot 2024-04-10 at 09.18.50.png]]

## Sharding and Hashing
Records are sharded to a number P of partitions typically based on Hashing.

A simple method is to define a key `k` that performs hashing and chooses as partition `hash(k)%P`

### Consistent Hashing
How can we handle sharding over a number of $M \neq P$ partitions?
- Adding more partitions
- Removing 1+ partition
Instead of mapping the hash with the module of partitions, we map the hash into a *ring of values*.


Under the simple sharding approach shown before every hash digest is mapped to a partition according to its `mod(P)`. Assuming P = 3
![[Screenshot 2024-04-10 at 09.22.27.png]]
If we add a new partition all data will have to be moved across partitions!
![[Screenshot 2024-04-10 at 09.22.42.png]]

In *consistent hashing* partitions are associated with the range of hashes. Each key is first hashed and the digest is “rounded up” to the max values of the sector. The record value is then associated with that partition.
![[Screenshot 2024-04-10 at 09.24.16.png]]

If a new partition is added, one does NOT have to move ALL data from ALL partitions.
Only the data from the neighbour partitions is moved.

Consistent hashing is also used for replication of data across many partitions. Given a replication factor of $n$: when a new record is stored in its appropriate partition, a copy is relayed on the $n-1$ closest partitions on the ring.


## NoSQL DBs (not only SQL)

NoSQL DB is a term introduced in 1998 to describe a DB relying on the SQL language.
With the rapid increase of large-data management requirements in the late 2000s, the noSQL term was generalized to define DBMS combinations of the following:
- Massively (horizontally) distributed systems
- Data Models other than relational
- Database language not relying on SQL

A list of common themes across most noSQL DBs:
- *Flexible Data Models*: typically have more flexible schemas compared to the relational model
- *Distributed Horizontal Scalability*: allows to scale-out horizontally with commodity hardware
- *Simple Queries*: as a rule of thumb, simpler queries (eg no JOINs) but usually faster than SQL
- *Looser Consistency Models*: most are not compliant with ACID

In contrast to the ACID transaction model of RDBs, NoSQL DBs tend to follow other transaction models, such as (but not only) **BASE**:
- *Basically Available*: individual nodes in the computer network are usually accessible most of the time
- *Soft State*: the DB state is not strictly persistent and can change without input (no strict consistency)
- *Eventually Consistent*: consistency is ensured only eventually, namely after a long enough time after the transaction

While ACID is very hard to implement in NOSQL, it’s important to realize no strictly defined transaction model applies to every NoSQL DB.

- Not all NoSQL DBs favor availability (AP) over consistency (CP)
- Both A and C can be provided in a gradient of degrees

It is not a black-or-white situation.

### Key-Value store

The simplest way of storing data: each record is a Value assigned to a Key
Key-value stores are *schema-less*
- Data can be stored in arbitrary formats
- No need for metadata to illustrate the data schema

To read/write/update the value, data is *looked up by key*.
Going without a schema makes key-value stores *very fast and easy to share across multiple nodes.
Often used to store data in memory for very fast record caching: *in-memory DB!*

Examples: Redis, Amazon Dynamo DB, MemCached…

### Document DB
Instead of storing key-value pairs as individual records, document-oriented DBs *store entire documents of pairs of fields and values*.
Each document is essentially a dictionary (usually a JSON):
- Flexible schema & ideal for semi-structured data
- Multiple documents can be fit into “collections” based on `_id`

There are *no JOIN*-like operations, but can be queried by document fields. Overall, a good choice for most applications when no schema is known a-priori.

Examples: MongoDB, CouchDB

### Other
Other notable DBs are:
**Time Series DB**: records are *data indexed by time-stamp*. Great for data produced by sensors and aggregated/queried based on time windows. Usually provide functionalities for computing metrics. (influxdb, Prometheus)
**FullText Search Engine**: Records are bodies of text as documents. Google-like *queries based on text return a ranking* of “best-fitting” documents. (ElasticSearch, Solr)


