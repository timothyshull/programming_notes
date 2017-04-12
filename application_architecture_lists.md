# NOTE: general overview with more to do here

# Replication
- leader-based replication (active/passive or master-slave)
- multi-leader (master-master or active/active)
- leaderless
- see also:
    - conflict-free replicated data types (CRDTs)

# Architecture
- client/server
    - client/server with multiple servers or load-balanced servers (proxy)
- peer-to-peer
- client-queue-client

# HA cluster modes
- active/active - always active on all nodes, service passed to any when using load balancing, with no load balancing just used to reduce failover time
- active/passive - 2 or more, only one master at a time
- n + 1 - 2 or more, when 2 degenerates to active/passive, n active, 1 passive for failover
- n + m - > 2, n active, m passive, used for many members to manage active services with two or more failover nodes
- n-to-1 - 2 or more, degenerates to active/passive with 2, uses the single standby only during recover of a failed active node
- n-to-n - > 2, similar to n-to-1 with one recovery node for each active node

# OSI Layers
1. physical
2. data-link
3. network
4. transport
5. session
6. presentation
7. application

# Architectural Models
- layered - abstractions sit on top of each other
- tiered - presentation (user interaction), application (business logic), data logic (storage)
- two tiered - user interaction layer communicates with server that handles app and data
- three tiered - user interacts with application which interacts with a database
- thin client
- proxy
- brokerage
- reflection
- (middleware)

# Communication
- socket -> (tcp or udp)
- marshalling -> convert a collection of data into a suitable form for message passing (serialization)
- unmarshalling -> disassembling
- corba, xml, java serialization, json, protocol buffers
- multicast
- network virtualization - overlay networks

# Remote Invocation
- request-response - structured messages
- rpc - requires explicit interfaces
- rmi (remote method invocation) - like rpc but uses remote objects with unique object references

# Indirect Communication
- group communication - message is sent to a group and the message is delivered to all members of the group (abstraction over multicast) (one-to-many)
- publish-subscribe - publish structured events to an event service which delivers (one-to-many)
- message queues - point-to-point to achieve space and time uncoupling
- shared memory - distributed shared memory
- tuple space communication

# EIP
## Integration Criteria
- coupling
- simplicity
- technology
- data format
- data timeliness
- data/functionality
- asynchronicity

## Integration Options
- file transfer
- shared database
- RPC
- messaging

## Patterns
### Message Construct
- Message
- Command Message
- Document Message
- Event Message
- Request-Reply
- Return Address
- Correlation Identifier
- Message Sequence
- Message Expiration
- Format Indicator
### Message Routing
- Pipes-and-Filters
- Message Router
- Content-based Router
- Message Filter
- Dynamic Router
- Recipient List
- Splitter
- Aggregator
- Resequencer
- Composed Msg. Processor
- Scatter-Gather
- Routing Slip
- Process Manager
- Message Broker
### Message Transformation
- Message Translator
- Envelope Wrapper
- Content Enricher
- Content Filter
- Claim Check
- Normalizer
- Canonical Data Model
### Messaging Endpoints
- Message Endpoint
- Messaging Gateway
- Messaging Mapper
- Transactional Client
- Polling Consumer
- Event-driven Consumer
- Competing Consumers
- Message Dispatcher
- Selective Consumer
- Durable Subscriber
- Idempotent Receiver
- Service Activator
### Messaging Channels
- Message Channel
- Point-to-Point Channel
- Publish-Subscr. Channel
- Datatype Channel
- Invalid Message Channel
- Dead Letter Channel
- Guaranteed Delivery
- Channel Adapter
- Messaging Bridge
- Message Bus
### Systems Mgmt
- Control Bus
- Detour
- Wire Tap
- Message History
- Message Store
- Smart Proxy
- Test Message
- Channel Purger


# Consensus (Replication protocols)
- Paxos (also cheap Paxos and fast Paxos)
- Raft
- Chandra-Toueg
- Lockstep protocol
- MSR-type algorithms
- hashgraph
- Zab (ZooKeeper Atomic Broadcast)
- viewstamped replication
- see also:
    - gossip
- bitcoin-related consensus
    - general goals
        - ensures next value (blockchain) is the single source of truth
        - keeps competing agents from modifying the set value in the context
    - proof of work*
        - proven by solving a cryptographic problem
    - proof of stake
        - proven by investing in unit of system
    - proof of activity
        - combines proof of work and proof of stake
    - proof of burn
        - commit units of system to an unusable state
    - proof of capacity
        - given priority by agent's memory space
    - proof of elapsed time


- NOTE: when considering Thrift vs. ZeroMQ
    - Thrift -> designed for use with streaming protocol (TCP)
    - ZeroMQ -> designed for messaging
    - consider which applications are better suited to which
