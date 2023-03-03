# Router Design

> What if Input Rate exceeds Output rate?
 
## Output Queueing
- Output interfaces buffer packets

- Pro
  - simple queuing algorithms
  - single congestion point

- Con
  - N inputs may send to the same output
  - Requires **speedup** of N
    - Output ports must be N times faster than input ports

> Note: Don't let buffer overflow! The overload should be temp

## Head-Of-Line Blocking

> TODO: revisit podcast to see slideshow demo 

## IQ + Virtual Output Queueing

- Input interfaces buffer packets in per-output virtual queues

- Pro
  - Solves blocking problem

- Con
  - More resources per port
  - Complex arbiter at switch
  - Still limited by input/output contention (scheduler)

## Virtual Output Queues

> TODO: revisit podcast to see slideshow demo

## Switch scheduling

- Problem
  - Match inputs and outputs
  - Resolve contentions, no packet drops
  - Maximize throughput
  - Do it in constant time...

- If traffic is uninformly distributed its easy
  - Lots of algorithms (approx matching)

- Seminal result (Dai et al, 2000)
  - Maximal size matching + *speedup* of two guarantees 100% utilization for most traffic assumptions

> Theory people use switch scheduling as a playground for testing out theory applications

> TODO: Revisit slides for notes on the slide I missed

## Basic Buffer Management

- FIFO + drop-tail
  - Simlest choice
  - Used widely in the Internet

- FIFO
  - Implies single class of traffic

- Drop-Tail
  - Arriving packets get dropped when queue is full regardless of flow or importance

- Important distinction:
  - FIFO: schedling discipline
  - Drop-Tail: drop policy

> Rudamentary policy choices. 

> TODO: Revisit slide change

## Key Router Challenges

- Buffer management
  - which packets to drop?
  - when?
  - due to finite lenght queues

- Scheduling
  - which packet to transmit next?

## FIFO/Drop-Tail Problems
- Leaves responsibility of preventing congestion completely to the edges
  - Transport protocols will need to make sure queues in routers are not so full that they drop (congestion control)

- Does not separate between different flows
  - Packets can be dropped regardless of what TCP or UDP flow it is part of

- **No Policing**: send more packets -> get more service

> full throttle send. Not a good incentive model!
> Internet relies on hosts to be nice. Relies on software on hosts/end users to not be aggressive.

- Synchronization: end hosts react to same problems

## Active Queue Management
- Design active router queue management to aid in reducing the congestion at routers

- Why?
  - Router has unified view of queueing behavior
  - Routers see actual queue occupancy (distinguish queue delay and propagation delay)
  - Routers can see if congestion is transient
    > Temporary bursts or hours-long traffic spike?

## Design Objectives
- Keep throughput high and delay low
  - High power (throughput/delay)

- Accommodate bursts

- Queue size should reflect ability to accept bursts rather than steady-state queueing (that's why they are there!)

- Help with transport protocol (e.g., TCP) performance with minimal hardware changes in router

> Videogame is actually a real market force. Prof was surprised that there's actual code to handle videogame specific traffic.

## Random Early Detection (RED)

> "Kinda nifty" --prof

- Detect incipient congestion

- ***Assume* hosts respond to lost packets**
  - we know they will retransmit based on losses
  - Soon we will see **losses will also make them slow down!**
    > spooks the hosts. Thinks the router is gonna be mad so it'll be nice and slow down :sweat_smile:

- Avoid window/end host synchronization
  - Randomly mark packets

- Do not bias against bursty traffic