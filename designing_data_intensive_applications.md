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
- used by
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