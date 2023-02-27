## Announcements/Aside

specs. Networks is the one field where you're gonna be given well defined implementation specs.

> Was a bit late, couldn't get the Overview

# 

## Autonomous Systems
- Internet is divided into **Autonomous Systems**
  - Distinct regions of administrative control
  - Routers/links managed by a single "institution"
  - Service provider, company, university, ...

- Hierarchy of Autonomous Systems
  - Large, "tier-1" provider with a nationwide backbone
  - Medium-sized regioal provider with smaller backbone
  - Small network run by a single company or university 

> Spectrum has a sep AS specifically for South Western US

- Interaction between Autonomous Systems
  - Internal topology is not shared between ASes
  - ... but, neighboring ASes interact to coordinate routing

## Inter-domain Routing
- Border routers summarize and advertise their routes to external neighbors and vice-versa
  - Border routers apply policy

- Internal routers can use notion of default routes

- Core is default-free: routers must have a route to all networks in the world

- But what routing protocol

> TODO: revisit slides. Copy over diagrams

## Issues with Link-State
- Topology info is flooded
  - high bandwidth and storage overhead
  - forces nodes to divulge sensitive info

- Entire path computed locally per node
  - TODO: COPY FROM SLIDES

- Minimizes some notion of total distance
  - TODO: COPY FROML SLIDES

- Typically only used INSIDE an AS
  - TODO

## Distance Vector *almost* there
- Advantages
  - Hides details of the network topology
  - Nodes deterine only "next hop towards theh destination

- Disadvantages
  - Minimizes some notion of total distance, which is difficult in an interdomain setting
  - Slow convergence due to the counting-to-infinity problem ("bad news travels slowly")

- Idea: Extend the notion of a distance vector
  - to make it easier to detect loops
  - Cost metric will be replaced 

## Path-vector routing
- Ext of distance-vector routing
  - Support flexible routing policies
  - Avoid count-to-infinity problem

- Key idea: advertise the entire path
  - Distance Vector: send *distance metric* per destination
  - Path Vector: send *entire path* for each destination
    > Recall you only send to neighbors, not flooding to entire network

> TODO: revisit slides. Copy diagram

## Loop Detection
- Node can easiliy detect a loop
  - Look for its own node identifier in path
  - e.g. node 1 sees itself in the path "3, 2, 1"

- Node can simply discard paths with loops
  - e.g. node 1 simply discards the advertisement

## Policy Support
- Each node can apply local policies
  - Path selection: which path to use?
  - Path export: which paths to advertise?

- Examples
  - Node 2 may prefer the path "2, 3, 1" over "2, 1"
  - Node 1 may not let node 3 hear the path "1, 2"

> Real world example. AS chooses a "longer" path to avoid sending packets through a country they don't have good relations with and don't trust.

> TODO: revisit slides. Copy diagram

## Border Gateway Protocol
- Interdomain routing protocol for the Internet
  - Prefix-based path-vector protocol
  - Policy-based routing based on AS Paths
  - Evolved during the past 28 years

```
- 1989: BGP-1 [RFC1105], replacement for EGP
- 1990: BGP-2 [RFC1163]
- 1991: BGP-3 [RFC1267]
- 1995: BGP-4 [RFC1771], support for CIDR
- 2006: BGP-4 [RFC14271, update
```

> Still based on trust

## Basic BGP Operation

- Border Router on AS1 ---> Border Router AS2
  - BGP session (TCP)
    - Establish session
    - Exchange all active routes
    - Exchange incremental updates

> TODO: revisit slides. Copy diagram

> While connection is ALIVE exchange route UPDATE messages

## Step-by-step
- A router learns multiple paths to destination
  - Stores all of the routes in a routing table
  - Applies policy to select a single active route
  - ... and may advertise the route to its neighbors

- Incremental updates
  - Announcement
    - Upon selecting a new active route, add own AS to path
    - ... and (optionally) advertise to each neighbor
  - Withdrawal
    - If the active route is no longer available
    - ... send a withdrawal message to the neighbors

## A Simple BGP Route
- Destination prefix (e.g. 128.112.0.0/16)
- Route attributes, including
  - AS path (e.g. "7018 88")
  - Next-hop IP address (e.g. 12.127.0.121)

> TODO: revisit slides. Copy diagram. 
> Also revisit podcast to hear explanation again

### Some BGP Attributes
- **AS Path**: (AS)s the announcement has traversed
- **Next-hop**: where the route was heard from
- **Origin**: Route came from IGP or EGP
- **Local pref**: Statically configured ranking of routes within AS
- **Multi Exit Discriminator**: preference for where to *exit* network
- **Community**: opaque data used for inter-ISP policy

> Goal: Policy Control (as opposed to optimization). Influenced by Politics, Money, etc.

## BGP Decision Process
- Default decision for route selection
  - Highest local pref, shortest AS path, lowest MED, prefer eBGP over iBGP, lower IGP cost, router id

> Note: it's not actually this picky, it's only this granular to break ties - which there will be a lot of if we only look at the stuff they care about

- Many policies buillt on default decision process, but...
  - Possible to create arbitrary policies in principle
    - Any criteria: BGP attributes, source address, prime number of bytes in message, ...
    - Can have separate policy for inbound routes, installed routes and outbound routes

  - Limited only by power of vendor-specific routing language

> TODO: revisit podcast to listen to the storytime. 

### Export Only Active Routes
- In conventional path vector routingn, a node has one **ranking function**, which reflects its routing policy

> TODO: revisit slides. Copy Diagram

### Example: Local Pref

- Set local pref score for each neighboring AS
  - higher values are more pref

> TODO: revisit slides. Copy Diagram

### Example: Shortest AS Path

> TODO: revisit slides. Copy Diagram

### AS Paths *vs.* Router Paths

Longer AS path COULD take shorter router paths

> It's about TRUST, MONEY, and POLITICS. 
> Internet routing has NOTHING to do with OPTIMALITY.

> TODO: revisit slides. Copy Diagram

## BGP has lots of problems
- Instability
  - Route flapping hurts (prefix goes up, down, up, down)
    - Lots of recalculation!
  - Not guaranteed o converge NP-hard to tell if it does

> Wind ==> flimsy fiber cable will go off and on. Think of wired headphones that have to be held JUST right
> Global Internet can get taken down by a STORM of announcements due to flapping

- Scalability still a problem
  - \> 500K network prefixes in default-free table today
  - Tension: Want to manage traffic to very specific networks
    - e.g. "mutihomed" context providers
  - But also want to aggregate information so you don't need info on all specific prefixes.

- Performance
  - Non-optimal, doesn't balance load across paths

## 

Read P&d 3.5 (router implementation)