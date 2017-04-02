# Ch. 1 Reliable, Scalable and Maintainable Applications
## Reliability
### Hardware faults
### Software errors
### Human errors
### How important is reliability
## Scalability
### Describing load
### Describing performance
### Approaches for coping with load
## Maintainability
### Operability: making life easy for operations
### Simplicity: managing complexity
### Evolvability: making change easy
## Summary
- application has requirements to be useful
- functional requirements
    - what application should do
    - data storage
    - data retrieval
    - data searching, processing, analysis
- non-functional requirements, e.g. properties
    - security
    - reliability
        - correct functionality even during faults
    - compliance
    - scalability
        - handling load gracefully
        - requires quantitative description of load
        - generally requires the ability to add processing capacity to remain
          reliable under load
    - compatibility
    - maintainability
        - improving quality of life for engineering and operations team
          working on an application
        - includes
            - understandable abstractions
            - complexity management
        - good operability
            - insight into system health
            - effective system health management

# Ch. 2 Data Models and Query Languages
# Summary
- many different models, huge subject
- historically
    - large tree
        - not good for many-to-many
    - relational model
        - some applications don't fit relational model
    - NoSQL/non-relational data stores
        - document databases - data stored in document and relationships between
          documents are minimal
        - graph databases - any data is potentially related to any other data
- relational, document, and graph are each good for a particular problem domain
  and awkward when applied to one of the other's respective problem domains
- no one-size-fits-all solution
- document and graph databases rarely enforce a schema
- query languages/frameworks
    - SQL
    - MapReduce
    - MongoDB aggregation pipeline
    - Cypher
    - SPARQL
    - Datalog
- CSS and XSL/XPath
- many other data models
    - sequence similarity searches require specialized genome databases like
      GenBank
    - massively large datasets often require custom solutions to reduce costs
    - full-text search -> information retrieval is its own problem domain


# Ch. 3 Storage and Retrieval
- storage and retrieval are most basic characteristic operations of a database
- generally two main workloads
    - optimized for transactions
    - optimized for analytics
- examine two families of storage engines
    - log-structured
    - page-oriented

## Data Structures that Power Your Database
- simple key-value store database
```
#!/bin/bash

db_set () {
    echo "$1,$2" >> database
}
db_get () {
    grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
}
```
- store has great performance
- lookup has terrible performance because it requires a full table scan
- for efficient find must use an index
    - store metadata in additional structure as a guidepost for finding data
    - adds overheads to writes because index structure must be updated
- many databases require the developer to choose the index manually to optimize
  benefit for current application

## Hash Indexes
- key-value or dictionary type
- indexing with hash map
    - in memory where every key is mapped to a byte offset within the data file
    - writes update hash map also
    - reads query hash map first
- Riak/Bitcask uses this approach
- useful for use case involving many writes
- since only appending to file at this point, run the risk of running out of
  disk space
- solution
    - break log into size segments
    - perform compaction on these segments
- compaction -> throw away duplicate keys in the log and only keep most recent
- generally also requires merging log segments
- since segments are never modified after written, merge is written to a new segment
- after merging is complete, update the log to read from the new segment and remove
  the old segment
- each segment then has its own in-memory indexing hash table
- to read a value
    - check most recent index for key
    - continue back through previous segments
- the described process also requires
    - file format -> text vs binary, if text, what structure (JSON, CSV, XML, etc)
    - record deletion -> append tombstone (special record to indicate deletion) to
      log file, delete on merge
    - crash recovery -> how to recover indexes from shutdown? can use snapshots
      to disk or some form of journaling
    - partially written records -> can use checksums to discover and remove partially
      written records on crash recovery
    - concurrency control -> commonly allow only one write and immutable nature of
      data files facilitates concurrent reads
- append only log is good for several reasons
    - sequential writes are generally much faster than random writes, especially
      on spinning disks but also generally on SSDs
    - concurrency and crash recovery is simpler
    - merging old segments avoids fragmentation
- limitations of hash table index
    - must fit in memory (can write to disk but causes random access I/O, costly
      to grow, and hash collisions are difficult to deal with)
    - range queries are not efficient

## SSTables and LSM-trees
- modify by requiring that writes to logs are sorted by key, i.e. use a sorted string
  table or SSTable
- also require each key only appears once within a merged file (handled by merges)
- advantages of sstables over logs with hash indexes
    - merging is simple and efficient (similar to mergesort)
    - no longer require an index of all of the keys in memory but instead can keep
      an index of some of the keys to provide an offset range and then scan
    - can compress segments to a block size when writing to save disk space and reduce
      I/O bandwidth of reads

### Constructing and maintaining SSTables
- easier to maintain in memory using red-black or AVL trees
    - on write, add to memtable or in-memory balanced tree structure
    - when size of memtable exceeds a limit, write to SSTable file and empty
      memtable
    - on read, check memtable, then most recent on-disk segment, then next most
      recent, etc.
    - occasionally run merging and compaction in background to combine segment files
      and discard overwritten or deleted values
- disadvantage:
    - crash will lose current memtable
    - to avoid, can maintain separate log of all writes on disk, recover memtable on
      crash recovery, and remove log on memtable write to disk

### Making an LSM-tree out of SSTables
- previous algorithm use essentially used in LevelDB and RocksDB
- similar algorithms used in Cassandra and HBase
- SSTables and memtables come from Google's Bigtable paper
- originally described as Log-Structured Merge-Tree (LSM-tree) which builds on
  log structured file systems
- Lucene (used by Elasticsearch and Solr) stores term dictionary in SSTable like files
    - key = word or term
    - value = posting list of IDs of all the documents that contain the word

### Performance optimizations
- LSM-trees are slow for nonexistent key lookups
    - solve by using a Bloom filter
- to determine order and timing of merge and compaction
    - size-tiered
        - newer and smaller SSTables are successively merged into older and larger
          SSTables
    - leveled
        - key range is split up into smaller SSTables and older data is moved into
          separate levels (allows compaction to proceed incrementally and uses less
          disk space)
- leveled
    - LevelDB
    - RocksDB
    - Cassandra
- size-tiered
    - HBase
    - Cassandra
- simple and effective, supports datasets much larger than available memory, supports
  high write throughput due to sequential disk writes

## B-trees
- most widely used indexing structure
- standard in almost all relational and many non-relational DBs
- breaks DB down into fixed-size blocks or pages, usually 4kB, which corresponds
  to underlying hardware (as opposed to the variable sized segments of log-structured
  segments)
- each node or page contains k keys and k+1 references to child nodes
- leaf nodes contain values
- traverse to leaf nodes through key range divisions
- on addition, split full pages into two half-full pages and update the parent
- B-trees maintain balance so n keys means a height of O(logn)

### Making B-trees reliable
- writes write to pages on disk as opposed to LSM-trees which never overwrite in place
- overwrite on disk
    - magnetic
        - move head of spinning platter and overwrite appropriate sector
        - SSD must erase and rewrite large block of chip at a time
- some operations require writing to many pages which can be dangerous due to
  potential for failure during write
- common to also maintain a write-ahead log (WAL or redo log)
    - append only log written to before B-tree modification
- must also carefully consider concurrent access
    - commonly done with latches (lightweight locks)
- log-structured approaches are simpler with respect to concurrency

### B-tree optimizations
- copy-on-write instead of WAL
- save space by abbreviating key (B+tree)
- pages can be positioned anywhere on disk, but more performance will be increased
  if leaf pages appear in sequential order on disk (difficult to maintain as size increases)
- add additional pointers to siblings for scan
- B-tree variants (fractal trees) use log-structured ideas to reduce disk seeks

## Comparing B-trees and LSM-trees
- generally
    - B-trees faster for reads
    - LSM-trees faster for writes
- benchmarks can be inconclusive

### Access patterns for writes
- B-trees must write twice, once to WAL and once to page
- overhead for writing entire page at a time
- LSM-trees write many times for a record on each merge/compaction (write amplification)
- LSM-trees are generally able to sustain higher write throughput
- LSM-trees can be compressed better (smaller on disk)
- B-trees can lead to fragmentation
- LSM-trees have lower storage overhead due to compaction and lower fragmentation
- SSDs firmware converts random writes to sequential writes so impact is less
  noticeable

### Downsides of LSM-trees
- compaction can affect read/write performance, sometimes making the performance
  of B-trees more predictable
- need to share disk bandwidth for initial writes and compaction writes
- compaction may not be able to keep up with rate of incoming writes
- need explicit monitoring to handle this
- B-trees do not duplicate keys
- best to test empirically for use case to determine between B-tree and LSM-tree

## Other indexing structures
- only discussed key-value, like primary key index in a relational model
    - points to
        - row in relational
        - document in document db
        - vertex in graph
- common to have secondary index
- main difference is that keys are not unique for secondary index
    - either make value list of matching row IDs
    - or make each key unique by appending row ID to it

### Storing values within the index
- can store actual target object (row, document, vertex) or a reference to an
  object stored elsewhere
    - heap file -> place to store target object elsewhere (maybe append only)
- can be efficient for updates
- can store indexed row directly within an index (clustered index)
    - can have secondary index refer to primary clustered index
- options
    - clustered index -> store all row data within the index
    - nonclustered index -> store only references to the data within the index
    - covering index (index with included columns) -> stores some of table's
      columns within the index
- clustering and covering indexes can speed up reads but require additional storage

### Multi-column indexes
- may need a key to map to multiple values
- concatenated index -> appends one column to another to retrieve all combinations
  of certain types (like a join but inherent in storage)
- multi-dimensional indexes -> more general method for querying several columns at once
- PostGIS uses R-trees to implement geospatial indexes
- many common applications for multidimensional indexes

### Full-text search and fuzzy indexes
- searching for similar keys (fuzzy querying)
- text search may include
    - synonyms
    - ignore grammatical variations
    - location within a document
    - edit distance
    - other linguistic analysis features
- Lucense uses a structure similar to a trie (FSA over characters of keys)
    - can be transformed into a Levenshtein automaton
- others use document classification and machine learning
    - see information retrieval textbook for more info

### Keeping everything in memory
- in-memory databases
    - Memcached (entirely in-memory)
    - VoltDB (relational)
    - MemSQL (relational)
    - Oracle TimesTen (relational)
    - RAMCloud (durable key-value store)
    - Redis (weak durability using async writes)
    - Couchbase (weak durability using async writes)
- some are not concerned with shutdown and loss
- some still write to disk as append only log for backup and serve reads
  entirely from memory
- performance advantage is from avoiding overhead of having to store data in a structure
  that can be easily written to disk
- can provide data models (e.g. priority queues and sets, etc) that are difficult to
  implement with disk-based indexes (Redis)
- anti-caching -> uses LRU eviction to write to disk and reloads on access
- anti-caching can be used to support datasets larger than memory
- anti-caching is similar to OS paging but is more efficient as DBs can work
  at a more granular level

## Transaction Processing or Analytics
- commercial transaction -> previously corresponded to write to database for businesses
- term transaction persisted
    - do not need ACID properties
        - atomicity
        - consistency
        - isolation
        - durability
    - transaction processing vs. batch processing
        - transactions correspond to reads and writes
- online transaction processing (OLTP) - interactive user input (i.e. CRUD in real time)
- data analytics use cases
    - scan huge data set and aggregate statistics
- online analytic processing (OLAP) - workload supporting data analytics over transactions
- OLTP
    - read pattern - small number of record queries fetched by key
    - write pattern - random access, low latency writes from user input
    - main use - end user/customer via (web) application
    - data represents - latest state of data at point in time of access
    - dataset size - Gb to Tb
- OLAP
    - read pattern - aggregate over large set of records
    - write pattern - bulk import (ETL) or event stream
    - main use - internal anlayst for decision support
    - data represents - history of events that occurred over time
    - dataset size - Tb to Pb
- in 80's and 90's trend towards analytics used separate database in what is called
  a data warehouse

## Data warehousing
- business may have many different transaction processing systems that must be
  highly-available
    - analysts should generally not operate on these
- data warehouse -> separate database(s) used to store relevant data from other
  systems for use by analysts
- ETL (extract-transform-load)
    - extracted from other systems using periodic dump or streaming
    - cleaned up for analysis
    - loaded into data warehouse DBs
- a large benefit is gained by using transformation to facilitate relevant queries

### The divergence between OLTP databases and data warehouses
- data analysis tools
    - drill-down
    - slicing and dicing
- Microsoft SQL Server and SAP HANA support transaction processing and data warehousing
  within the same product
- commercial vendors
    - Teradata
    - Vertica
    - SAP HANA
    - ParAccel
- open source SQL-on-Hadoop solutions to compete
    - Apache Hive,
    - AMPLab's SHark
    - Cloudera Impala
    - Hortonworks Stinger
    - Facebook Presto
    - Apache Tajo
    - Apache Drill
- some use ideas from Google's Dremel

## Stars and snowflakes: schemas for analytics
- many data warehousing techniques are formulaic
    - star schema aka dimensional modeling
    - centered on fact table or the table that reflects an event or a transaction
    - some columns in fact tables are foreign key references to other tables
      (dimension tables)
    - dimension tables describe events (who, what, when, where, why, how)
- star schema -> fact table is in the middle and references dimension tables as
  point of the star
- snowflake schema
    - dimensions are further broken down into subdimensions
    - snowflake schemas are more normalized than star schemas but star schemas
      are easier for analysts to work with
- fact tables may be several hundred columns wide

## Column-oriented storage
- large datasets (trillions of rows and petabytes of data) are difficult to store
  and query
- common queries only access a few columns at a time (out of many hundred)
- this requires loading all of the rows from disk to memory even with multiple
  keys
- column-oriented storage -> rather than storing all of the values from a row
  together, store all of the values from a column together which can save work
- can be relational or non-relation
- Parquet -> columnar storage based on Google's Dremel

### Column compression
- data is more easily compressed due to similar data type and reduces disk throughput
  demands
- number of distinct values in a column is generally also small (due to repetition)
- NOTE:
    - column-oriented storage and column families
        - column families -> comes from Bigtable but used by HBase and Cassandra
        - store all columns from a row together along with a row key
        - do not use compression
        - as a result, mostly row-oriented

### Memory bandwidth and pipelined execution
- must consider memory cache performance
- avoiding branch mispredictions and bubbles in CPU instruction processing pipeline
- utilizing vectorized instructions such as SIMD
- column compression allows for more values to fit in cache
- can be iterated and processed in tight loop

### Sort order in column storage
- can choose another sort order and use as an indexing mechanism
- doesn't make sense to sort independently because loose the original relationship
  to entry's row
- can batch sort by rows at a time
- sort order can help with compression because highly repetitive columns as primary
  sort column can reduce compressed size

### Several different sort orders
- C-Store and adopted in Vertica
- can use replicas to store in a separate sort order

### Writing to column-oriented storage
- column-oriented storage is good for that OLAP use case
- not good for many writes
- LSM-trees are a good solution
    - store writes in memory
    - merge at merge point with column-oriented files on disk

### Aggregation: Data cubes and materialized views
- many data warehouse use row-oriented storage
    - columnar storage is gaining popularity
- materialized aggregates -> storage of many aggregate queries (count, sum, average,
  min, max, etc)
- materialized view -> a table-like object whose contents are the result of some
  query
- like a standard (virtual) view
- on data update, must be repopulated due to dernomalized view of data
- don't necessarily make sense in OLTP DBs, make sense in OLAP DBs
- data cube (OLAP cube) -> gird of aggregates grouped by different dimensions
    - each cell contains the aggregate data for a particular combination of the
      dimensions from a fact table and are repeatedly summarized along each of
      the dimensions
- makes certain queries very fast
- reduces flexibility from querying raw data
- most data warehouses use raw data for most queries and reserve data cubes for
  heavily used, performance heavy queries

## Summary
- two broad categories for storage and retrieval
    - OLTP
    - OLAP
- log-structured
    - only append to files
    - delete obsolete files
    - use SSTables and LSM-trees
    - Bitcask
    - LevelDB
    - Cassandra
    - HBase
    - Lucene
- update-in place
    - B-trees
    - treats disk as fixed-size pages which can be overwritten
    - most relational DBs and many non-relational DBs
- data warehouse architecture
- column oriented storage

# Ch. 4 Encoding and Evolution

# PART II Distributed Data
- reasons for use of multiple machines
    - scalability
        - spread load for reads or writes across multiple machines if it is greater
          than what can be handled by a single machine
    - fault tolerance/high availability
        - use multiple machines for redundancy to handle faults or failures by allowing
          another to take over
    - latency
        - latency imposed by bad networks or geographic distance can be mediated by having
          a machine that is part of the application in a location that is closer

## Scaling to Higher Load
- if processing power/load increase is all that is needed, can just use a more
  powerful computer (vertical scaling or scaling up)
- shared-memory architecture
    - combining multiple CPUs, RAM chips, and disks together under one operating
      system with a fast interconnect to treat as a single machine
- has a linear cost but due to bottlenecks may not be able to handle load in a linear
  proportion to scaling
- offers limited fault tolerance
- shared-disk architecture
    - CPU and RAM form a single machine but storage is on an array of disks that
      is shared between machines
    - increases fault tolerance and storage but is generally not scalable beyond
      that

### Shared-Nothing Architectures
- aka
    - horizontal scaling
    - scaling out
- each machine is a node with independent hardware and coordination is performed
  at a software level
- can choose machines by best price/performance ratio because no special hardware
  is needed
- can use VMs for cloud deployments
- a lot to be aware of in shared-nothing architecture for application developers

### Replication vs. Partitioning
- replication
    - storage of a copy of the same data on several different nodes for redundancy
      and performance
- partitioning
    - splitting data in a data store into smaller subsets of the data (partitions)
      so they can be assigned to different nodes (sharding)


# Ch. 5 Replication
- keeping multiple copies of same data
   - geographic location (reduce latency)
   - fault tolerance and availability
   - increase throughput by increasing number of machines that can serve
     queries (scale out)
- assumes each dataset can be stored on a single machine (and discuss partitioning
  later)
- replication is easy for immutable data sets
- difficulty lies in handling changes
- three popular algorithms
   - single-leader
   - multi-leader
   - leaderless
- considerations
   - synchronous vs. asynchronous replication
   - handling of failed replicas
- studied since 70's
- many misunderstandings for application developers

## Leaders and Followers
- replica -> node with a copy of data
- main question -> how to ensure all data updates get propagated to all replicas?
- leader-based replication (aka active/passive or master-slave)
   - replica designated as leader (aka master or primary)
   - write requests sent through leader
   - leader sends updates to followers (aka read replicas, slaves, secondaries, hot
     standbys) as a replication log or change stream
   - follower udpates by running all writes in order of replication log
   - reads can be sent to leader or any follower
 used by
   - relational
       - PostgresQL (since 9.0)
       - MySQL
       - Oracle Data Guard
       - SQL Server's AlwaysOn Availability Groups
   - non-relational
       - MongoDB
       - RethinkDB
       - Espresso
   - distributed message brokers
       - Kafka
       - RabbitMQ
   - networked file systems/replicate block devices
       - DRBD

## Synchronous vs. asynchronous replication
- important and often configurable
- sync
   - leader waits for a successful ack from first follower before reporting success
   - advantage
       - follower is always guaranteed to have up-to-date copy
   - disadvantage
       - write cannot be processed in case of replica or network failure and system
         will become blocked
   - only makes sense for one of the followers to be synchronous
- async
   - leader fires and forgets change stream
   - advantage
       - leader can process writes even in case of replica or network failure
         between leader and replica
   - disadvantage
       - if leader fails, writes are not recoverable so not durable

 ## Setting up new followers
 - increase number of replicas or replace failed nodes
 - cannot simply copy
 - could lock before copy but this remove HA requirement
 - conceptual process to avoid downtime
   - snapshot leader's DB at some pint in time (without locking)
   - copy snapshot to follower
   - follower comes online and requests replication log from point of snapshot
     (log sequence number or binlog coordinates)
   - after processing backlog, replica continues as any other follower would

## Handling node outages
- reboots and crashes occur
- achieving HA with leader-based replication

### Follower failure: catch-up recovery
- follower logs data changes on disk
- to recover, follower supplies leader with last log sequence point and leader
  provides an update

### Leader failure: failover
- promote follower to leader position
- manual or automatic
- process
   1. determine that leader has failed -> can use a heartbeat/timeout
   2. choose a new leader -> leader election algorithm (consensus problem) or chosen
      by previously determined controller node (generally chooses most up-to-date
      replica)
   3. reconfigure system to new leader -> set clients and followers to send requests
      to new leader, handle old leader coming back online by assigning as a follower
      and updating data
- common failover issues
    - for async replication, new leader may not be up-to-date
      (may discard old leaders writes but there are other solutions)
    - discarding writes can be dangerous
    - can result in two nodes believing they are leaders (split brain) and may not
      be able to resolve conflicts between both leaders accepting writes
    - proper timeout before electing a new leader -> failover too often or not often
      enough can degrade performance in situations where the system is likely already
      struggling to handle performance
- all point to fundamental distributed systems problems
    - replica consistency
    - durability
    - availability
    - latency

## Implementation of replication logs
- presents common leader-based replication methods

### Statement-based replication
- leader logs each insert, update, or delete request and forwards to followers,
  where followers parse each as a client request
- disadvantages:
    - non-deterministic functions (like reading time) will not be the same
    - counters necessitate updates in exact same order which may be difficult
      to maintain for multiple concurrently executing transactions
    - side-effects may not result in the same state on each replica unless
      forced to result in deterministic side-effects
- there are workarounds, but other replication methods are preferred
- used in MySQL prior to 5.1
- used by VoltDB but forces transactions to be deterministic

### Write-ahead log (WAL) shipping
- exist as part of log-structured storage engine
- written to before page writes in B-tree based storage engine
- append only sequence of bytes indicating all writes to DB
- used in
    - PostgreSQL
    - Oracle
    - others

### Logical log replication
- use different log formats for replication than those used by storage engine
- logical vs physical log
- describes writes at granularity of a row
    - insert -> all values for row
    - delete -> ID for row to remove
    - update -> ID for row and values of all columns or only the updated values
- easier to maintain backwards compatibility, allowing for different software
  versions
- easier for other software to parse (for data warehousing, etc)

### Trigger-based replication
- may need more flexibility to manage what data is replicated
- makes sense to move replication to application layer
- trigger (stored procedure) -> register custom application code to DB so it is
  triggered on some DB event (i.e. a write) (can be used to transfer data to OLAP DB)
    - Databus for Oracle
    - Bucardo for PostgreSQL
- adds overhead to replication

## Problems With Replication Lag
- for mostly read workloads (common pattern on web)
    - create many followers and distribute reads across these followers
- results in read-scaling or load balancing
- only realistically works with async replication
    - synchronous replication would hang with a node failure or network outage
      and large systems are almost guaranteed to have some sort of failure
- reads from async followers may have outdated info (inconsistencies)
- eventual consistency -> consistency property in async replication systems where
  at a single instant in time, replicas may not be consistent with each other, but
  stopping writes for some (unspecified) amount of time will cause the system to
  return to a consistent state
- not only NoSQL
     - followers in an asynchronously replicated relational database have the
       same characteristics

## Reading your own writes
- with async replication, if user writes (through leader) and reads immediately
  after from follower, may not see their own write
- read-after-write consistency (read-your-writes consistency) -> guarantees a user
  will always see their own writes in an async replication system
- options for implementing in a leader-based async replication system
    - when reading a value the user may have modified, read from leader
        - requires knowing what the user may have modified without querying
        - can resort to always reading user's own data (profile, etc) from leader
     - for applications where a user can modify most things, can instead
       track last update (to system overall) and default to reading from leader if
       last update occurred within a preset time duration
     - can also track last write by user and check last update to follower's replica,
       resorting to leader if follower is not up-to-date (requires distributed
       timestamping system using logical timestamps)
- geography (i.e. routing through datacenters) adds complexity
- additional issues to consider for cross-device access by a user
    - need to centralize last write timestamp information for a user
    - connections from different devices may not be routed through same datacenter
      an thus interaction with the same follower

## Monotonic reads
- leader-based with async replication can result in user seeing things going
  backwards in time when making several reads from different replicas with varying
  degrees of lag
- monotonic reads -> guarantee that several reads in sequence will not result in
  later reads reporting older data than previous reads (weaker guarantee than
  strong consistency and stronger guarantee than eventual consistency)
- one method - always read from same replica

## Consistent prefix reads
- in cases where there is a causal relationship between data written to a database
  over time (i.e. chat exchange with sequential responses) requires another guarantee
- consistent prefix reads -> if a sequence of writes happens in a certain (causal)
  order then anyone reading those writes will see them appear in the same order
- common in partitioned/sharded databases
- in general, requires a distributed transaction with a guarantee such as snapshot
  isolation

## Solutions for replication lag
- need to consider increasingly long lags
- applications can provide stronger guarantees than the underlying database
- many distributed databases have abandoned transactions in favor of eventual consistency
- claim is that the need to rely on eventual consistency within a scalable system is
  inevitable
- this is not true (see Ch 7)

## Multi-leader replication
- leader-based replication results in a single point of failure
- multi-leader configuration (aka master-master replication or active/active)
    - allow more than one node to accept writes
    - each node that accepts writes must forward data to all other nodes
    - each leader simultaneously acts as a follower to other leaders

### Use cases for multi-leader replication
- generally not useful in a single datacenter

### Multi-datacenter operation
- can have a leader in each datacenter
- within each datacenter, regular leader-follower replication is used
- comparison
    - performance
        - inter-datacenter delay for inter-leader replication is hidden from
          user so perceived performance may be better
    - datacenter outage tolerance
        - single leader -> failover of single leader in outage can cause a performance hit
        - mult-leader -> each datacenter can continue operating independently of others
    - tolerance of network problems
        - inter-datacenter traffic is over the public net and is slower and less reliable
        - single leader is sensitive to this
        - multi-leader with async replication handles much better
    - can be implemented with external tools
        -  Tungsten Replicator for MySQL
        - BDR for PostgreSQL
        - GoldenGate for Oracle
- often considered dangerous and should be avoided if possible

### Clients with offline operation
- many desktop/mobile apps like calendar need offline operation
- local database acts as leader
- may have very long replication lag
- essentially the same as multi-leader replication
- tools designed for this
    - CouchDB
    - (PouchDB)

### Collaborative editing
- Etherpad
- Google docs
- share many of the same distributed database problems
- for complete conflict freedom guarantee a lock must be used
    - equivalent to single-leader replication with transactions on the leader
- can make unit of change very small to avoid locks
    - brings challenges of multi-leader replication and conflict resolution

## Handling write conflicts
- multi-leader replication allows write conflicts

### Synchronous vs. asynchronous conflict detection
- single leader -> second write blocks and waits or aborts their own transaction
  and retires
- multi-leader -> both writes succeed and conflict is detected at some later point
    - can make conflict detection synchronous but loses main advantage of multi-leader
      replication (allowing each leader to accept writes independently)

### Conflict avoidance
- easiest to avoid (all writes for an application go through a single leader)
- if there is a need to change the leader for any reason (failure etc) must
  deal with the possibility of concurrent writes on different leaders

### Converging towards a consistent state
- single-leader -> writes a committed in a sequential order
- multi-leader -> no defined ordering to writes, so database can end up in inconsistent
  state
- method for convergent conflict resolution
    - ID writes (timestamp/UUID) and prefer highest ID (last write wins or LWW)
        - prone to data loss
    - give each replica an ID and prefer writes at a higher numbered replica
        - prone to data loss
    - merge written data in some way
    - record conflict in a structure that preserves all information and add handling
      to resolve conflict at some later time

### Custom conflic resolution logic
- many multi-leader replication tools expose conflict resolution customization hooks
    - on write
        - handler cannot prompt user, is generally run in background, and must run
          quickly
    - on read
        - application stores conflicts and may prompt user to resolve on read or
          resolve automatically and write back to DB
        - CouchDB

### Automatic conflict resolution
- conflict-free replicated data types (CRDTs)
    - family of data structures (sets, maps, ordered lists, counters, etc) which
      can be modified concurrently and automatically resolve conflicts (see Riak 2.0)
- mergeable persistent data structures
    - track history (like git) and use a three-way merge function (CRDTs use two-way
      merge) (see Merkl trees)
- operational transformation
    - algorithm behind Google docs
    - manages concurrent editing of an ordered list of items, e.g. characters composing
      a text document
- all are relatively young algorithms but likely they will mature and grow as they
  are used in more applications

### What is a conflict?
- subtle conflicts
    - single usage conflicts (no overlapping usage of a resource) occurring at same time

## Multi-leader replication topologies
- replication topology -> describes path along which writes are propagated from one
  node to another
- examples
    - circular
    - star
    - all-to-all
- writes may pass through several nodes before reaching all replicas
- nodes are given UUID
- circular and star topologies can be interrupted by node failure
- all-to-all topologies demonstrate better fault tolerance
- all-to-all topologies can have messages overtake others due to differences in network
  latency between nodes
    - results in problem of causality similar to consistent prefix reads
- ordering of events properly can be solved with a technique called version vectors
- read system docs and be aware of issues that may arise with multi-leader replication

## Leaderless replication
- leader determines order of writes and followers replicate in same order
- leaderless allows any replica node to accept writes
    - Dynamo
    - Riak
    - Cassandra
    - Voldemort
- Dynamo-style databases
- some allow clients to send writes to multiple replicas at once
- some use a coordinator (controller node) to forward writes
    - coordinator does not enforce a particular ordering

- NOTE: Dynamo is different from DynamoDB -> Dynamo is internal and DynamoDB is external
  and has a different architecture

### Writing to the database when a node is down
- leaderless configurations do not have a concept of failover
- client sends write to all replicas in parallel
- uses quorum writes and quorum reads to manage leaderless reads and writes to handle
  the possibility of a failure
- requires read repairs after outages

### Read repair and anti-entropy
- two mechanisms used in Dynamo-style datastores to restore nodes to a consistent state
  after an outage
    - read repair -> client can detect stale responses from reads from several replicas
      and writes newer value back to replica
        - works well for frequently read values
    - anti-entropy -> background process that constantly looks for differences in data
      between replicas (not ordered like log process, may be significant delay)
- some systems use only one

### Quorums for reading and writing
- generally
    - for n replicas, need w writes and r reads from replicas
    - as long as w + r > n can expect to get an up-to-date value when reading
        - at least one of the r nodes reading from must be up-to-date
    - reads and writes that obey these r and w values are called quorum reads and writes
    - view r and w as the minimum number of votes required for the read or write to be valid
- typically configurable in Dynamo style databases and various settings can result
  in different read vs. write throughput
- w + r > n facilitates fault tolerance because
    - if w < n writes can still be processed on node failure
    - if r < n reads can still be processed on node failure
    - various settings of odd numbers of n and 1/2 or greater than 1/2 of r/w
      allows 1 to 2 nodes to go down and still allow reads/writes
    - normally both reads and writes are sent to all n replicas in parallel
      quorum is only used ack back
- error on fewer than w or r nodes available

### Limitations of quorum consistency
- edge cases exist with above quorum formula where stale values may be returned
  for reads
    - sloppy quorum -> writes may end up on different nodes than reads so value
      overlap is not guaranteed
    - two concurrent writes must be merged because LWW can result in dropping
      writes due to clock skew
    - write and read concurrently, write may only go to some replicas and returned
      read value is unclear
    - write succeed on some and fail on others below quorum (i.e failed write) the
      result might not get rolled back and read may not return write value
    - if a node that is part of quorum fails before update across cluster the quorum
      condition may fail
    - more edge cases even in healthy functioning
- for w + r <= n, stale values are more likely but increases availability and lowers
  latency
- Dynamo-style databases are optimized for use cases that tolerate eventual consistency
- stronger guarantees require transactions or consensus

### Monitoring staleness
- need to monitor for health
- easier to monitor in leader-based systems
- in leaderless
    - no fixed order for writes
    - if only read repair is used, no limit to how old a value might be
- makes monitoring more difficult
- very important to be able to quantify eventuality in eventually consistent
- research in area

### Sloppy quorums and hinted handoff
- quorums can tolerate network delay and node failure (to a certain extent)
- how to handle failed quorum (when data has a location to which it is designated)?
    - error
    - write to quorum subset
- sloppy quorum -> still require a quorum as above but the successful responses can
  be from nodes for which the data is not destined
- on repair, the data must be sent to designated nodes aka hinted handoff
- sloppy quorum is not a quorum but more an assurance of durability
- sloppy quorums are optional in all common Dynamo implementations
    - Riak - enabled by default
    - Cassandra and Voldemort - disabled by default

### Multi-datacenter operation
- leaderless replication is well-suited for multi-datacenter replication
- Cassandra and Voldemort use the normal leaderless model
    - clients usually wait for quorum ack within its own datacenter
    - higher latency writes to other datacenters happen asynchronously in background
- Riak performs cross-datacenter replication in a style similar to multi-leader
  replication

## Detecting concurrent writes
- Dynamo-style allows multiple concurrent writes to same key, resulting in conflict
    - also in read-repair and hinted handoff
- most DBs handle the convergence towards eventually consistency poorly

### Last write wins (discarding concurrent writes)
- only store recent and overwrite older values in each replica
    - requires unambiguous mechanism for determining most recent
- not correct to discuss concept of most recent in context of concurrency because
  order is undefined
- instead, force arbitrary order using timestamp and LWW
- achieves eventual consistency at the cost of durability
    - multiple successful concurrent writes will only result in a single write
- if data loss is unacceptable, LWW is not a good choice
- best solution (with Cassandra, etc) is to ensure a key is only written once
  (thus is immutable) possibly by using a UUID as the key so each write operation
  receives a unique key

### The "happens-before" relationship and concurrency
- to determine concurrency (by intuition)
    - think of causally dependent relationships between writes
        - have an implicit ordering
    - concurrent writes cannot have a causally dependent relationship because
      neither client knows of the other's action
- three possibilities for events A and B
    - A -> B
    - B -> A
    - A and B are concurrent

### Concurrency, time, relativity
- difficult problem due to clocks in distributed systems
- simplify to classifying two operations as concurrent if they are unaware of
  each other

### Tracking happens-before relationships
- see text for description of problem and solution algorithm

### Merging concurrently written values
- merging sibling values is similar to conflict resolution in multi-leader replication
- can use LWW but this is often not good enough
- can use set unions for siblings but does is complicated by removal because
  if only one client removes the other will see a value previously removed
- system must maintain a deletion marker for a key as a tombstone
- CRDTs are applicable here as well

### Version vectors
- using a single version number is not acceptable when multiple replicas are
  accepting writes concurrently
- version vector -> a per replica, per key version number instead
- most interesting is Riak's dotted version vector
- facilitates
    - distinguishing overwrites
    - distinguishing concurrent writes
    - sibling merge
    - read and write from separate replicas
- version vectors are similar to vector clocks
    - version vector -> client server systems
    - vector clock -> peer-to-peer systems

# Summary
- purposes of replication
    - HA
    - disconnected operation
    - managing latency
    - scalability
- approaches
    - single-leader
    - multi-leader
    - leaderless
- sync and async replication
- consistency models for handling replication lag
    - read-after-write
    - monotonic reads
    - consistent prefix reads
- concurrency issues

# Ch. 6 Partitions
- aka
    - shard
    - region
    - tablet
    - vBucket
- the result of splitting data replicas to handle large datasets or handle high
  query throughput
- generally each piece of data only belongs to a single partition
- done to facilitate scalability
- shared-nothing architecture -> horizontal scaling or scaling out, each machine
  or VM is a distinct node, with independent CPU, RAM, disks, etc, and coordination
  is done at a software level (i.e. through messaging) rather than sharing resources
- not related to network partitions
- pioneered in 80's with Teradata and Tandem NonStop SQL

## Partitioning and replication
- commonly combined
- node may store more than one partition (various configurations)

## Partitioning of key-value data
- spread query load
- data may be skewed where one node receives more queries than others (hot spot)
- random distribution of keys is simplest resolution but causes a need to query
  all nodes for data since do not know where the data is

### Partitioning by key range
- can determine which node contains a key the range it is responsible is known
- can manually set partitions or automatically
- partition boundaries adapt to data
- used by
    - BigTable
    - HBase
    - RethinkDB
    - MongoDB (before 2.4)
- can keep keys in sorted order within partitions
- key range partitioning can lead to access patterns that cause hot spots
- timestamps are susceptible to problems in this scheme

### Partitioning by hash of key
- good hash function uniformly distributes keys across partitions
- MD5
    - Cassandra
    - MongoDB
- Fowler-Noll-Vo
    - Voldemort
- (also Murmur 3 and City Hash)
- can assign range of hashes to a partition
- ranges can be evenly spaced or chosen pseudo-randomly
- lose ability to do efficient range queries
- in MongoDB, sends range queries to all partitions
- not supported in Riak, Couchbase, and Voldemort
- Cassandra compromises
    - table can be declared with a compound primary key consisting of several columns
    - only first part of key is hashed to determine partition
    - other columns are used to concatenate index for sorting the data in SSTables
    - cannot scan range on first part, but if first part is set, can scan range on
      remainder
    - facilitates one-to-many relationships

### Consistent Hashing
- method for evenly distributing load across internet-wide system of caches such as CDN
- chooses random partition boundaries to avoid need for central control or distributed
  consensus
- use of word consistent here is different from other uses in book
- doesn't work well for databases

### Skewed workloads and relieving hot spots
- hashing does not completely avoid hot spots
- one technique is to add random number to beginning or end of hot key to split across
  many keys
- affects reads because reads then have to do more work

## Partitioning and secondary indexes
- very useful for data modeling, common in relational databases, some document DBs,
  and has been added to Riak
- Solr and Elasticsearch function solely for secondary indexes
- don't map neatly to partitions
- solutions
    - document-based partitioning
    - term-based partitioning

### Partitioning secondary indexes by document
- each partitions maintains the secondary index for the data within that partition
  and is not concerned with secondary indexes in other partitions
- known as local index
- reading a document-partitioned secondary index does not guarantee to return a full
  set of data based on the secondary index
- scatter/gather -> send secondary index to all partitions and combine returned results
- scatter/gather is a solution but is expensive
- used in
    - MongoDB
    - Riak
    - Cassandra
    - Elasticsearch
    - SolrCloud
    - VoltDB
- suggest to structure partitioning scheme so secondary index queries can be served
  from a single partition

### Partitioning secondary indexes by term
- can construct global index that covers data in all partitions, but it must also
  be partitioned
- can be partitioned differently
- term-based partitioning
- can make reads more efficient by avoiding scatter/gather
- can add write overhead
- updates to global secondary indexes are often asynchronous
- used in
    - DynamoDB
    - Riak
    - Oracle data warehouse

## Rebalancing partitions
- database change
    - query throughput increases, so add more CPUs to handle load
    - dataset size increases, so more disks and RAM for storage
    - machines fail and other machines handle new responsibilities
- moving data for these reasons is called rebalancing
    - updates load to balance load
    - database should continue to handle reads and writes during rebalance
    - rebalance should minimize data transfer

### Strategies for rebalancing
### How not to do it: hash mod N
- number of nodes changing modifies N so most data needs to be moved making
  rebalancing very expensive

### Fixed number of partitions
- create more partitions than nodes and store many partitions on each node
- allows for stealing partitions on node addition
- only entire partitions are transferred
- can also assign more partitions to more powerful machines to balance load
- used in
    - Riak
    - Cassandra since 1.2
    - Elasticsearch
    - Couchbase
    - Voldemort
- can split and modify partitions, but fixed number of partition databases
  usually avoid this so best to choose a number of partitions high enough to handle
  future growth

### Dynamic partitioning
- key-range partitioning facilitates dynamic partitioning
- when partition grows beyond a configured size, partition is split and vice versa
- similar to how B-trees work
- used by
    - HBase (uses underlying HDFS)
    - RethinkDB
- adapts to total data volume
- empty DB starts with one partition which defeats the purpose
- HBase and MongoDB allow an initial pre-split configuration to set the number of
  minimum partitions
- can work equally well with hash-partitioned data

### Other rebalancing strategies
- before 1.2, Cassandra used consistent hashing with pseudo-random partition boundaries
- suffered from poor load distribution and made it difficult to add nodes
- replaced with fixed partitioning after
- most widely used approaches
    - hashing with a fixed number of partitions
    - dynamic partitioning by key range

## Request routing
- which node to connect to for request by client?
- relates to service discovery problem (not limited to databases)
    - any piece of software running on a network has this problem
    - especially highly available software
- many in-house solutions
- approaches
    - clients contact node (via round robin load-balancer) either handles or forwards
      appropriately
    - send all requests to routing tier which forwards, acting as partition-aware
      load balancer
    - client receives node-partition mapping and handles requests
- difficult problem (related to consensus)
- ZooKeeper often used to keep mapping of partitions to nodes
- other actors can subscribe to read mapping
- uses ZooKeeper
    - Espresso -> Helix
    - HBase
    - SolrCloud
    - Kafka
- MongoDB uses config server and mongos daemons for routing
- uses gossip protocol
    - Cassandra
    - Riak
- gossip allows clients to connect to any node, removing reliance on external
  coordination service, but adds more complexity to database node responsibility
- Couchbase does not rebalance automatically
- still requires IP discovery for machine connections, but can use DNS for this purpose

### Parallel query execution
- most NoSQL distributed data stores are limited to supporting simple queries
- massively parallel processing (MPP) relational databases can support complex
  queries and are often used for analytics
- business analytics makes data warehouse queries an important topic

## Summary
- spread data and query load evenly across multiple machines, avoiding hot spots
- main approaches
    - key range partitioning
    - hash partitioning
    - (hybrid approaches)
- secondary index partitioning
    - document-partitioned index
    - term-partitioned index (global index)
- query routing for partition-aware load balancing or sophisticated parallel query
  execution
- partitions generally operate independently but operating on many partitions
  requires difficult reasoning

# Ch. 7 Transactions
- many things can go wrong in a data system
    - software or hardware
    - crash
    - network interruptions
    - multiple conflicting concurrent writes
    - corrupted data
    - race conditions
- implementing fault tolerance requires units on which one can reason about complex
  data systems
- transactions -> means to group several reads and writes into one logical unit or operation
    - commit -> transaction success
    - abort (rollback) -> transaction failure
- transactions simplify the programming model
- facilitate conceptual and theoretical safety guarantees
- investigation of transactions will include look at concurrency control and isolation
  levels
    - read committed
    - snapshot isolation
    - serializability

## The slippery concept of a transaction
- most relational and many non-relational follow concept introduced in 1975 by IBM
  System R, first SQL database
    - MySQL
    - PosgreSQL
    - Oracle
    - SQL Server
- many NoSQL databases abandoned the conceptual model of transactions or redefined them
    - some view transactions as the source of what makes a system unscalable
    - some view transactions (and related guarantees) as a prerequisite for
      serious applications handling valuable data
- both are overstatements and transactions have advantages and disadvantages

### The meaning of ACID
- ACID
    - atomicity
    - consistency
    - isolation
    - durability
- safety guarantees provided by transactions
- in practice, each definition is malleable and "ACID compliant" is a label that is
  not necessarily clear
- BASE (systems used to refer to systems that do not meet ACID criteria)
    - basically available
    - soft state
    - eventually consistent

### Atomicity
- atomic -> something that cannot be broken down into smaller parts
- in threading, if one thread performs an atomic action means there is no way
  another thread can see an intermediate state
- in ACID, not about concurrency
- in ACID, refers to guarantees about state after a transaction
    - if a transaction is composed of many writes and the system fails in the middle
      then the system guarantees the state of the writes will not be persisted
- centered on notion of transaction and guarantees about state if a conceptually
  atomic transaction is failed or aborted

### Consistency
- overloaded word in the contexts discussed in this book
    - replica consistency and eventual consistency
    - consistency = linearizability
    - for ACID -> application-specific notion of a database being in a functionally
      sound state
- idea is that data has invariants and after any transactions there is still a
  guarantee these invariants will always be satisfied
- dependent on the application's notions of invariants and guarantees
    - e.g. how to handle bad data from user? is any resulting inconsistency
      beyond the scope of these guarantees
- atomicity, isolation and durability are properties of the database
- consistency (in the ACID sense) is a property of the application (so may not fit with
  acronym)

### Isolation
- concurrently executing transactions are isolated from each other and will not affect
  the resulting state of the database
- isolation = serializability in classical theoretical sense
- in practice, serializable isolation causes performance issues and is rarely used

### Durability
- guarantees that, once a transaction has been successfully been committed, it will
  never be lost or removed even if there is a hardware fault or database crash
- usually equivalent to commit to disk but also includes logging and crash
  recovery
- can include replication
- no such thing as perfect durability

### Replication and durability
- write to disk vs. replication as the definition of durable
    - write to disk and machine death = unavailable, replication would stay
      available in face of a machine crash
    - correlated fault (power outage or bug) can result in all replicas being
      unavailable, writing to disk is still a necessary guarantee
    - SSDs have been shown to lose data on a sudden power outage
    - data storage engine interactions with the filesystem has been shown to
      result in subtle bugs outside of data storage engine that corrupt data
      (esp after crash)
    - disk data can become corrupted which can propagate through replicas
    - 30-80% of SSDs develop at least one bad block, magnetic drives have a higher
      rate of failure but are less susceptible to corruption
    - SSDs can lose data when not powered indefinitely
- take theoretical guarantees with a grain of salt

### Single-object and multi-object operations
- atomicity and isolation describe proper behavior for multi-write transactions
    - atomicity -> error partway through should be rolled back (all-or-nothing)
    - isolation -> concurrent transactions should not affect each other
- assumes several objects may be modified at once
- multi-object transactions require the ability to identify source and interval/duration
- many non-relational databases do not provide a mechanism for grouping operations

### Single-object writes
- large amounts of data (that take time to modify) still require same guarantees
    - network connection
    - power failure
    - concurrent reads/writes
- some databases provide atomic (in the sense of multithreaded programming) operations
- these atomic operations are not transactions in the usual sense of the word
    - sometimes referred to as lightweight transactions or ACID
- to compare, a transaction is a mechanism for grouping multiple operations
  on multiple objects into one unit of execution

### The need for multi-object transactions
- many distributed datastores have abandoned multi-object transactions
- required uses
    - in relational model, foreign key references require modification in a single
      transaction
    - graph databases require modifications on vertices connected by edges
    - in document database, a document generally only requires one operation for
      updates, but joined documents require denormalization and updates to
      denormalized information require updating multiple documents in one transaction
    - secondary indexes require update
- without transactions, error handling becomes much more complex

### Handling errors and aborts
- transactions allow for abortion and retries
- others (esp leaderless replication) follow the model of doing as much as possible
  but not undoing when an error is encountered
- popular ORMs (Django, Rails) do not retry aborted transactions
- retries are susceptible to
    - failed acks (network etc) which may result in client retrying manually
    - errors from overload are worsened by retries and can result in feedback
      cycles
    - retrying permanent errors is useless
    - side-effects outside of DB may persist even in failure
    - data will be lost if process fails while retrying

## Weak isolation levels
- race conditions -> concurrent read and write, concurrent write and write
- databases try to hide concurrency issues from application developers with
  transaction isolation
    - theoretically provides serializability
- serializable isolation has a performance cost
    - many databases don’t want to pay that price
- common to use weaker isolation
- concurrency issues have caused
    - money loss
    - financial audit
    - data corruption
- some ACID databases provide weak isolation
- need strong understanding of concurrency issues to adequately understand and
  avoid them

## Read committed
- most basic with two guarantees
    1. only see data that has been committed for reads (no dirty reads)
    2. on write, will only overwrite committed data (no dirty writes)
- (also read uncommitted -> no dirty writes but does allow dirty reads)

### No dirty reads
- read during incomplete transaction -> should be prevented
    - incomplete multi-object updates may cause reader to see inconsistent
      state
    - aborted multi-object updates may result in reading modifications that
      are later rolled back

### No dirty writes
- avoids concurrency problems
    - incomplete multi-object updates may cause inconsistent states
    - does not prevent counter updates

### Implementing read committed
- default setting
    - Oracle 11g
    - PosgreSQL
    - SQL Server 2012
    - MemSQL
    - others
- writes most commonly implemented using row-level (unique) write locks
- can use read locks to avoid double reads but affects performance
- most databases prevent dirty reads by retaining old values during long running
  writes and displaying the new value to users only after the write transaction
  has completed

### Snapshot isolation and repeatable
- read skew can result in reads that show an inconsistent view (example is transfer
  from one table to another and reading between completion of first write and before
  completion of second write)
- skew is an overloaded word -> means timing anomaly here
- non-repeatable read or read skew is acceptable under read committed isolation
- it is unacceptable during
    - backups
    - analytic queries and integrity checks
- snapshot isolation is the most common solution
- each transaction must read from a consistent snapshot of the database
- snapshot isolation aids reasoning about isolation
- supported in
    - PostgreSQL
    - MySQL
    - Oracle
    - SQL Server
    - more

### Implementing snapshot isolation
- uses write locks to prevent dirty writes also but for reads does not use locks
    - readers never block writers
    - writers never block readers
- must maintain many versions of the data at any given time to handle dirty reads
- known as multi-version concurrency control (MVCC)
- sufficient to only keep two versions for read committed isolation
- implementation in PostgreSQL (similar to others)
    - when a transaction is started, it is given an increasing UUID
    - whene a transaction writes anything to the database, the data it writes is
      tagged with the transaction ID of the writer
    - each row in a table has a created by field, containing the ID of the transaction
      that inserted this row into the table
    - each row has a deleted by field, which is initially empty
    - if a transaction deletes a row, the row isn’t actually deleted from the
      database
        - it is marked for deletion by setting the deleted by field to the ID of the
          transaction that requested the deletion
    - when no transaction can access the deleted data, it is garbage collected

### Visibility rules for observing a consistent snapshot
- database can ensure consistent snapshots by carefully defining visibility rules
  for transactions based on transaction IDs
    1. on transaction start, tracks each currently running transaction
    2. writes made by aborted transactions are ignored
    3. writes with transaction ID after current transaction are ignored even if
       later write has already been committed
    4. all other writes are visible to the application's queries
- apply to both creation and deletion
- object is visible if both
    - at the time when the reader’s transaction started, the transaction which
      created the object had already committed
    - the object is not marked for deletion — or if it is, the transaction which
      requested deletion had not yet committed at the time when the reader’s
      transaction started

### Indexes and snapshot isolation
- many details to multi-version concurrency control in practice
- PostgreSQL has optimizations for avoiding index updates if different versions of
  the same object can fit on the same page
- append-only/copy-on-write for B-trees
    - CouchDB
    - Datomic
    - LMDB

### Repeatable read and naming confusion
- snapshot isolation is useful especially for red-only transaction
- known by different names
    - serializable -> PostgreSQL
    - repeatable read -> MySQL
- historically the SQL standard has confused these naming conventions and usages

## Preventing lost updates
- many concurrent write issues
- lost update problem
    - two concurrent read-modify-write cycles can lead to second clobbering first
- occurs when
    - incrementing a counter or a balance
    - modifying a complex value with some numeric operation (i.e. value in list in
      JSON doc)
    - concurrent page edits (to a web based document, etc)

### Atomic write operations
- atomic updates avoid implementing read-modify-write cycles
- SQL UPDATE is safe in most databases
- MongoDB provides atomic document updates
- atomic operations are usually implemented using an exclusive lock
- aka cursor stability
- can also force all atomic operations to be performed on a single thread
- ORMs expose the ability to write read-modify-write cycles that do not take
  advantage of DBs atomic operations

### Explicit locking
- can be used for updates to objects
- difficult to get right and can result in race conditions

### Automatically detecting lost updates
- can allow read-modify-write cycles to execute in parallel and a transaction manager
  can detect aborted transactions and force retries
- used in
    - PostgreSQL -> repeatable read
    - Oracle -> serializable
    - SQL Server -> snapshot
- not used in
    - MySQL/InnoDB
- less error prone than locking

### Compare-and-set
- sometimes provide atomic compare-and-set in place of transactions
- check safety on a per database basis before relying on it

### Conflict resolution and replication
- replicated databases require additional steps to avoid lost updates
- multi-leader and leaderless databases can have multiple concurrent writes to
  different nodes and cannot rely on locks or compare-and-set
- one technique is allowing writes to create several versions (siblings)
  and resolve conflicts after the fact
- can use atomic operations in a replicated context (esp commutative atomic operations)
- LWW is prone to lost updates

### Preventing write skew and phantoms
- two race condition types
    - dirty writes
    - lost updates
- many more subtle bugs

### Characterizing write skew
- occurs if two transactions read the same objects and then update some of those objects
  (different transactions can update different objects)
- generalization of lost update
- solutions are more restricted
    - atomic single-object operations don't work because many objects are involved
    - automatic lost update detection in some snapshot isolation doesn't help because
      requires true serializable isolation
    - some DBs allow configurable constraints but not always easy to solve with
      constraints
    - best option may end up being explicit locks

### More examples of write skew
- related to topological sorting constraints
- booking systems, multiplayer games and physics constraints, concurrent resource
  claims

### Phantoms causing write skew
- common events in write skew (maybe different order)
    1. a SELECT query
    2. continue or abort based on result of 1.
    3. on continue, make a write (INSERT, UPDATE, or DELETE) and commit transaction
    4. start over and receive different result from SELECT
- could lock on 1. but when 1. does not return a result there is nothing to lock
- phantoms -> one transaction changes the result of a search query in another transaction

### Materializing conflicts
- takes a phantom and turns it into a lock conflict on a concrete set of rows that
  exist in the database
- difficult and error prone - should be seen as a last resort

## Serializability
- situation to this point
    - isolation levels are hard to understand and inconsistently implemented
    - large application code can be difficult to reason about and results of
      current isolation level may not be apparent
    - static analysis can help find race conditions but many concurrency problems
      are non-deterministic
- research answers that solution is serializable isolation (strongest isolation level)
- guarantees that concurrent execution can be mapped to a particular serial execution
    - in other words database prevents all possible race conditions
- options explored
    1. actual serial execution
    2. two-phase locking
    3. optimistic concurrency control such as serializable snapshot isolation
- each has problems
- consider from single node and then examine generalizations

### Actual serial execution
- simplest solution is to remove concurrency
- only became feasible due to
    - reduced RAM cost to keep active dataset in memory
    - OLTP transactions are usually short and make a small number of reads
      and writes
- used in
    - VoltDB/H-Store
    - Redis
    - Datomic
- (also consider the approach of Node.js)
- can compete in performance due to avoidance of locking overhead but has a limited
  throughput

### Encapsulating transactions in stored procedures
- rather than waiting for a user, databases encapsulate transactions
  and generally commit on a single HTTP request
- systems with single-threaded serial transaction processing don’t allow interactive
  multi-statement transactions
- the application submits the entire transaction code to the database ahead of time
  as a stored procedure
    - instead of querying and writing, the whole transaction is a chunk of code with
      the logic for handling

### Pros and cons of stored procedures
- existed for some time
- each has own language
    - Oracle -> PL/SQL
    - SQL Server -> T-SQL
    - PostgreSQL -> PL/pgSQL
- languages are generally pretty cumbersome and archaic
- harder to debug, version control, test, and integrate with metrics
- databases are very performance sensitive and bad stored procedures can cause serious
  bottlenecks
- many modern databases use general-purpose programming languages for stored procedures
    - VoltDB -> Java or Groovy
    - Datomic -> Java or Clojure
    - Redis -> Lua
- can achieve good throughput on a single trhead
- VoltDB allows stored procedures to define the replication policy

### Partitioning
- single thread execution limitations can be scaled to run on separate partitions
- steps that require multiple partitions need to coordinate across partitions which
  adds overhead
- the ability to do this at all can depend on the structure of the data being stored

### Summary of serial execution
- transactions must be small and fast
- limited to use cases where active dataset can fit in memory (can also use LRU-style
  caching to swap some data to disk to augment requirement)
- write throughput must be acceptably low or data needs to be able to be partitioned
  to require little to no cross-partition coordination
- cross-partition transactions are possible, but there is a hard limit to the extent
  which they can be used

## Two-phase locking (2PL)
- previously the main algorithm used for database serializability
    - completely different from two-phase commit
- several transactions can read the same object as long as it is not modified
- writes require exclusive access
    - uses mutual exclusion
    - also requires that writers block readers but readers never block writers
    - provides serializability (not provided by snapshot isolation)

### Implementation of two-phase locking
- used int
    - MySQL (InnoDB)
    - SQL Server
    - DB2
- lock has modes
    - shared mode
    - exclusive mode
- readers acquire lock in shared mode and wait for only exclusive mode locks to
  release
- writers acquire lock in exclusive mode and wait on any shared or exclusive
  mode locks to release
- readers can convert shared mode locks to exclusive mode locks
- all locks are held to end of transaction
- deadlock detection is automated and triggers one thread holding a lock to release
  and retry

### Performance of two-phase locking
- waiting and lock contention reduce performance
- long running transactions add to potential bottlenecks caused by locks
- can have unstable throughput, latencies, and be unstable at high percentiles
- deadlocks are more common with 2PL serializability

### Predicate locks
- serializable isolation requires the prevention of phantoms
- can be used by many objects that satisfy some condition, even those that
  do not yet exist
- restricts access as follows
    - transaction A reads objects matching a condition by acquiring a shared-mode
      predicate lock and must wait for any existing exclusive locks
    - to modify any objects, A must check for any existing matching predicate locks
      and wait for any to be released

### Index-range locks
- aka next-key locking
- simplifies predicate locks by locking on a larger range of objects
- any modifications to overlapping ranges requires waiting
- can fall back to shared locks for cases where a query cannot be satisfied by a
  particular range

## Serializable snapshot isolation (SSI)
- promising solution to performance hits caused by other serializable solutions
- 2008 Michale Cahill PhD thesis
- used in
    - PostgreSQL 9.1 ->
    - other distributed databases

### Pessimistic vs. optimistic concurrency control
- pessimistic
    - like mutual exclusion -> if anything might go wrong, wait until situation
      is safe again
- serial execution is pessimistic to an extreme
- optimistic
    - don't block -> continue as normal
    - check if isolation was violated in retrospect and retry if necessary
    - only allow transactions that executed serializably
- old idea
- performs poorly under high contention
- performs much better than pessimistic approaches under low contention
- uses snapshot isolation
- adds algorithm for detecting serialization conflicts and aborting accordingly

### Decisions based on an outdated premise
- write skew in snapshot isolation requires reading and deciding action based
  on result of read
- transaction takes action based on a potentially outdated premise
- SSI must detect situations where a transaction takes action based on an outdated
  premise and abort the transaction
    1. detect stale MVCC reads (uncommitted write occurred before the read)
    2. detect writes that affect prior reads (the write occurs after the read)

### Detecting stale MVCC reads
- reads from consistent MVCC snapshot ignore uncommitted writes
- if the premise on which a transaction commits is invalid by the time it commits
  SSI must abort the transaction
- must also support long running reads, which is what complicates the situation
  and keeps SSI from aborting as soon as the stale read is detected

### Detecting writes that affect prior reads
- writes must look for currently executing reads and notify the transactions
  that the data they are currently reading may be out of date

### Performance of serializable snapshot isolation
- granular tracking may reduce performance but less granular tracking may lead to
  more aborted writes
- sometimes stale reads are OK
    - used by PostgreSQL to reduce aborts
- SSI removes need for locking in 2PL in cases
    - read-only queries can run without locks
    - improves variability in performance
    - good for read-heavy workloads
- can distribute cost of serialization across many CPUS (unlike serial execution)
    - done by FoundationDB
- rate of aborts affects performance of SSI

# Summary
- abstraction layer that reduces many considerations down to aborting the transaction
- abandoned in many NoSQL DBs
    - can result in inconsistent data on error
- isolation levels
    - read committed
    - snapshot aka repeatable read
    - serializable
- race conditions
    - dirty reads
    - dirty writes
    - read skew (non-repeatable reads)
    - lost updates
    - write skew
    - phantom reads
- only serializable isolation protects against all of the above issues
    1. actual serial execution
    2. two-phase locking
    3. optimistic concurrency control such as serializable snapshot isolation

# Ch. 8 The Trouble with Distributed Systems
- distributed systems engineering is fundamentally different than systems engineering
  on a single machine

## Faults and Partial Failures
- software running on a single, properly functioning computer should be deterministic
  so issues are bugs with the software and not issues with the system
- distributed systems operate within a model that has to assume much worse than ideal
  conditions (the network and physical world do not operate as idealized theoretically)
- partial failure -> one portion of system is broken in some unpredictable way even though
  the rest of the system may be working as expected
- partial failures are non-deterministic
- these two things (partial failures and non-determinism) are what make distributed
  systems difficult to work with

## Cloud computing and supercomputing
- high-performance computing (HPC) -> used for computationally intensive (often
  scientific) computing tasks
- cloud computing -> (not well-defined) related to multitenant datacenters, commodity
  hardware connected via IP network, elastic/on-demand resource allocation, and
  metered billing for scalable resources access
- traditional datacenters lie in between
- supercomputers are more like a single computer than a distributed system and are
  often built from specialized hardware
- nodes are generally reliable and communicate through shared memory and remote
  direct memory access (RDMA)
- use specialized network topologies such as multidimensional meshes and toruses
- book focuses on systems for implementing internet services
    - online -> service users at any/all times
    - commodity hardware
    - utilize large datacenter networks which are often based on IP and Ethernet
      in Clos topologies to provide high bisection bandwidth
    - increase in size of system increases likelihood of a broken component
    - tolerance to failed nodes is important (e.g. rolling upgrades, node replacements)
    - geographically distributed systems often requires public internet
- must accept possibility of partial failure and build fault tolerant systems
- beneficial to test for the widest range of possible faults explicitly (chaos
  testing/engineering)

### Building a reliable system from unreliable components
- many computing systems have error-correcting codes
- TCP can reassemble packets
- despite the possibility for error correction, there is always a bound on reliability
  that can be achieved when considering reliability of components

## Unreliable Networks
- focus on shared-nothing systems
- asynchronous packet networks -> internet and most internal networks, a node can send
  a packet but network provides no time or correctness guarantees
- possible errors
    1. dropped request
    2. stalled in a queue for delivery
    3. receiver may have failed/crashed
    4. receiver may have temporarily stopped responding
    5. dropped response
    6. delayed response
- generally handled with timeouts but still do not know how to handle delayed case

### Network faults in practice
- network faults are common even in controlled environments
- network partitions -> occurs when one part of the network is cut off from the rest
  due to a network fault (aka netsplit or network fault)
- software must account for possible network faults but does not have to tolerate them

### Detecting faults
- systems need to detect faulty nodes
    - load balancer should remove a faulty request from retry rotation
    - single-leader replication leader failure should result in follower promotion
- common situations
    - can reach target machine but port is not open or service is not running the OS
      should respond with TCP RST or FIN packet
    - node process crashed or was killed by admin should send alert to trigger some
      form of failover
    - link failure at hardware level of network switches (can be queried for private
      network but not public internet)
    - router can reply with ICMP Destination Unreachable packets but router does not
      know of many of the same failures as above
- may never receive error responses

### Timeouts and unbounded delays