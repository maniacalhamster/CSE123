# Sliding Window

Single mechanism that supports
- Multiple outstanding packets (pipelined transmission)
- Reliable delivery
- In-order delivery
- *flow control*: Don't send more than receiver can handle

Sender/Receiver each maintain a "window" abstraction to track outstandign packets
- CORE of all modern ARQ protocols

> Sender: Keep track of SENT packets (but not acknowledged)
> Receiver: Keep track of RECEIVED packets (but not processed)

Go-Back-N is a special case
- receive window size of one

## Sender

`....][Last ack][  ][  ][  ][  ][  ][  ][  ][  ][  ][  ][Last sent][....`

Window bounds outstanding unACKed data
- implies need for buffering at sender

"Last" ACK applies to **in-order** data

What to do on a timeout?
- Go-Back-N: resend all unackowledged data on timeout
- Selective Repeat: individual timeout/packet; resend as needed

## Receiver

`....][Last received][  ][  ][  ][  ][  ][  ][  ][  ][  ][Largest Accepted][....`

Receiver buffers packets that arive...
- ...*out-of-order*, they are stored to provide in-order
- ...*faster* than can be consumed by higher layers
    > **Flow control**: tell sender how much receive window is left in ACK 
    - Sender will only send enough packets to fill receive window        
    - Case: maxed out on window size; the next time it's free, RESEND the last ACK with the new window size

# Deciding when to retransmit
How does sender know when a packethas been lost?
- Ultimately, sender uses timeouts to decide when to retransmit

But how longshould the timeout be?
- Too long: inefficient (large delays, poor use of bandwidth)
- Too short: may retransmit unnecessarily (causing extra traffic)

Right timer is based on link latency: **round-trip time** (RTT)
- Which can vary greatly for reasons we'll see later
- Latest transport protocols (e.g. Google QUIC) have sophisticated schemes for computing timeout for retransmissions

> RTT for China is 20ms (exponentially longer than CPU bus rates)

Time includes
1. Seralizing bits takes time
2. Propogation time: time to send on Physical layer

## Can we shortcut the timeout?

Timeout is long in practice

If packets are usually *in ORDER* then *out-of-order* packets or ACKS imply that a packet was lost

Knowing something is lost, we can be proactive!
- Negative ACK (receiver side informs of missing packet)
    > receiver requests missing packet
- Fast retransmit (sender side)

TODO: Fill in from slides

## Fast Retransmit

Don't bother waiting for timeout
- Receipt of duplicate acknowledgement (`dupACK`) indicates loss
- Retransmit immediately

Used in TCP
- Needs to be careful if frames can be reordered
- Today's TCP identifies a loss if there are **three** duplicate ACKs in a row

> On average the out of sequence-ness is around 2 to 3 packets late. Not 10+ late. So by the time 3 packets FAILED to fix the out of order, we assume it was lost.

TODO: rewatch this section (10:40-10:45) since I zoned out

# Overview of all Transport Layer Tasks

Define and provide specific delivery semantics
- which endpoint (which application/process)
- When
- How
- If

Multiplexing different processes between the same hosts

... and Reliability (if the application needs it)

## and what's next

Process naming/demultiplexing within a host

Overview of two popular transport layer protocols
- User Datagram Protocol (UDP)
    > unordered and unreliable delivery

- Transport Control Procotol
    > TODO: get from slides

## Establishing well-known ports

