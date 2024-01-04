# Routing Protocols

Routing Information Protocol (RIP)

* interior protocol, distance vector, runs on UDP, high convergence

Open Shortest Path First (OSPF)

* interior, cost vector

Intermediate System to System (IS-IS)

* interior, cost
* only classless routing protocol

EIRGP (Cisco, hybrid system, advanced vector)

Border Gateway Protocol (BGP)

* external
* path vector by autonomous system hops(like the ISP's it go through) instead of router hops
* most widespread, does not converge quickly

**Split Horizon**: Anti routing loop made for distance vector

**Poison Reverse**: Anti routing for link state, by making the reverse connection to costly

## Spanning Tree Protocol (STP)

### Root and Nonroot Bridges

* Root: Switch with lowest bridge id elected as root bridge, BID made by priority value
* Nonroot bridge: all others

### Ports

* Root port: on every non root bridge, port closest to root bridge(cost wise, then lower port no)
* Designated port: all ports on root bridge, port closest to root bridge
* Non designated port: ports that block traffic to create loop free

### Port States

Non designated ports doo't forward traffic but BPDUs (Bridge protocol data units)

## Multicast

Internet Group Management Protocol(IGMP)

* used by servers to let routers know who are multicast receivers
* 3 versions 1 is annoying every 60s, 2 client can send leave mssg, 3 can request multicast from only specific server

Protocol Independent Management (PIM)

* Dense mode: flood and prune behavior to form optimal network tree, not great performance
* Sparse mode: initially starts off somewhere but eventually becomes great
* Creates Shortes Path Tree(SPT)
