replication -
leader-based replication (active/passive or master-slave)
multi-leader (master-master or active/active)
leaderless

architecture -
client/server
    client/server with multiple servers or load-balanced servers (proxy)
peer-to-peer
client-queue-client

ha cluster modes - 
active/active - always active on all nodes, service passed to any when using load balancing, with no load balancing just used to reduce failover time
active/passive - 2 or more, only one master at a time
n + 1 - 2 or more, when 2 degenerates to active/passive, n active, 1 passive for failover
n + m - > 2, n active, m passive, used for many members to manage active services with two or more failover nodes
n-to-1 - 2 or more, degenerates to active/passive with 2, uses the single standby only during recover of a failed active node
n-to-n - > 2, similar to n-to-1 with one recovery node for each active node

osi layers -
7 - application
6 - presentation
5 - session
4 - transport
3 - network
2 - data-link
1 - physical

architectural models - 
layered - abstractions sit on top of each other
tiered - presentation (user interaction), application (business logic), data logic (storage)
two tiered - user interaction layer communicates with server that handles app and data
three tiered - user interacts with application which interacts with a database 
thin client
proxy
brokerage
reflection
(middleware)

communication - 
socket -> (tcp or ump) -> 
marshalling -> convert a collection of data into a suitable form for message passing (serialization)
unmarshalling -> disassembling
corba, xml, java serialization, son, protocol buffers
multicast 
network virtualization - overlay networks

remote invocation -
request-response - structured messages
rpc - requires explicit interfaces
rmi (remote method invocation) - like rpc but uses remote objects with unique object references

indirect communication -
group communication - message is sent to a group and the message is delivered to all members of the group (abstraction over multicast) (one-to-many)
publish-subscribe - publish structured events to an event service which delivers (one-to-many)
message queues - point-to-point to achieve space and time uncoupling
shared memory - distributed shared memory
tuple space communication
