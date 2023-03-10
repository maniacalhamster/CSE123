# HW4 notes

## Q1 - RED
- More of a Reading Check on Section 6.4.1!
  - more in-depth than lecture
- Router drops a few packets before buffer space is exhausted completely (signals src to slow down)
- computes an avg queueu length rather than an instantaneous one 
  - more accurately captures the notion of congestion (samples could have bursts and lulls)
- Smoother transition from random dropping to complete dropping is more appropriate
  - RED has 2 queue length thresholds that trigger certain activity
    - MinThreshold
    - MaxThreshold
  1. calculate TempP
  2. calculate P
     - ensures packet drops are distributed over time
     - only one drop per round-trip time is enough to cause a connection to reduce its window size, whereas multiple drops might send it back into slow start
    
## Q2 - Traffic Shaping w/ Token Bucket

- calculate the serialization delay for one packet 
  - i.e. using the capacity **R** of the outgoing link from token bucket shaper, calcuate the time for one packet to exit the shaper)
- After each packet is transmitted, calcualte the number of tokens left in the bucket 
  - There will be some tokens used up and some tokens filled up as well

- For 2.1: calculate the number of tokens left by repeating the above step for the required number of times
- For 2.2: Keep calculating the number of tokens left after each packet is sent. You'll reach a point when you don't have enough tokens
- For 2.3: Wait for token bucket to get filled such that there is just enuogh tokens to transmit the queued packet

## Q3 - Fair Queueing and Weighted Fair Queueing

- Most important part of question is to figure out the **f** (fair share value)

Ex:
  - Suppose you have to divide 32 Mbps among 4 flows of 12, 5, 8, 15 Mbps using fair queueing
    - Let f = 32/4 Mbps
    - 1: gets 8
    - 2: gets 5
    - 3: gets 8
    - 4: gets 8
    - total: 29 (3 left over)
  - Adjust f = (32 - 5 - 8)/2 = 9.5
    - 1: gets 9.5
    - 2: gets 5
    - 3: gets 8
    - 4: gets 9.5
    - total: 32 (**done**)
  
For weighted fair queueing, set the intial f by dividing the total bandwidth by sum of the weight of flows. After each allotment adjust by dividing bandwidth by sum of weight of remaining flows.

## Q4 - TCP Congestion Control

Read question carefully solution is straighforward if you correctly understand:
  - slow start
  - congestion avoidance
  - setting of congestion threshold
  - number of segments sent

Terms
- Slow start: cwnd increases as `1MSS 2MSS 4MSS 8MSS...` until sstresh is reached
Congestion avoidance: cwnd increases as `x, x+1 MSS, x+2 MSS, x+3 MSS` 
sstresh is set euql to half current cwnd value before timeout
Sender sends segments with min(cwnd, rwnd) bytes

## Q5 - Router Design

Lecture 3/3 is sufficient to solve 

---

# PA4 Overview

Changes compared to PA3
- When forwarding packets, use longest prefix match to choose a routing table entry
  - replace exact match implementation which was done in PA3
  - Make use of subnet mask in the rtable entries and the **&** operator to do the matching (AND mask to apply it)

- ARP Caching
  - Before sending an ARP request, use the ARP cache to see if you find an entry there. (**sr_arpcache_lookup**)
    - If found, use it
    - Else, send request. On reply, info from ARP reply can be queued (**sr_arpcache_insert**)
    - the arp cache timeout is already done by a background thread

- Additional ICMP messages
  - Destination net unreachable, Destination host unreachable, Port unreachable, Time exceeded

## PA4 Roadmap

1. Commplete LPM and ARP caching using PA3 topology
2. Work on additional ICMP error types
3. Follow instructions in writeup to set up multi-router topology
   - Note: you'll need 4 terminals now instead of 3
   - Should be able to scale to many more routers too
4. Tools to test your code
   - `ping`
   - `traceroute`
   - `wget`
  
> LPM will not be tested on existing PA3 topology since they're 3 separate networks w/ `/24` netmasks. Change the mask or add entries to test out LPM implementation on PA3 topology