## MPLS: Fundamentals of L3VPN

![Topology Diagram](https://github.com/tomammon/network-configurations/blob/master/cisco-mpls-l3vpn/cisco-mpls-l3vpn.png)

**Platform: Cisco IOS-XE (CSR1000V 16.5.2)**

### Notes
  * Each LSR is configured with a custom label range to illustrate the locally-significant nature of label advertisements

### CE5
```
CE5#sh ip bgp 172.16.67.0
BGP routing table entry for 172.16.67.0/24, version 5
Paths: (1 available, best #1, table default)
  Not advertised to any peer
  Refresh Epoch 1
  105 6
    10.1.35.3 from 10.1.35.3 (3.3.3.3)
      Origin IGP, localpref 100, valid, external, best
      rx pathid: 0, tx pathid: 0x0

CE5#sh ip route 172.16.67.0
Routing entry for 172.16.67.0/24
  Known via "bgp 5", distance 20, metric 0
  Tag 105, type external
  Last update from 10.1.35.3 00:05:03 ago
  Routing Descriptor Blocks:
  * 10.1.35.3, from 10.1.35.3, 00:05:03 ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 105
      MPLS label: none

CE5#sh ip route 10.1.35.3
Routing entry for 10.1.35.0/24
  Known via "connected", distance 0, metric 0 (connected, via interface)
  Advertised by bgp 5
  Routing Descriptor Blocks:
  * directly connected, via GigabitEthernet0/0
      Route metric is 0, traffic share count is 1

CE5#sh ip cef 172.16.67.0
172.16.67.0/24
  nexthop 10.1.35.3 GigabitEthernet0/0
```

### CE6
```

```

### PE3
```

```

### PE4
```

```

### P1
```

```

### P2
```

```
