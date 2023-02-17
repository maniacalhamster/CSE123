> Missed the first 10 min of lecture
> Started notes @10:12AM

## Address Types
- Each interface has multiple diff addresses
  - Link Local prefixed with FE80:://10 (1111 1110 10)
  - Unique Local prefixed with FC00:://7  (1111 110)
    - Used only internal to one network
  - Global 
    - Like an IPv4 addr

> TODO: revisit slides

## Addressing Summary
- You can't route efficiently on flat address space
  - Table size = all hosts on internet
  - Table updates --> everyone

- Network-layer addressing is done hierarchically
  - Routing prefix + host suffix
  - Originallyu, host/network split was done statically (class-based)
  - Now it is done dynamically (CIDR)
  - Allows contiguous chunks of address space to be aggregated (for purpose of routing) into fewer prefixes
  - Requires moe complex forwarding table lookup (LPM)

> routers use TKAM: very expensive, very fast memory --- O(1) speed LPM, amazing technology but EXPENSIVE

## Route Aggregation
- Combine adjacent networks in forwarding tables
  - Helps keep forwarding table size down

> TODO: Copy diagram here

