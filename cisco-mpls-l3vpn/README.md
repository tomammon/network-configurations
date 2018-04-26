## MPLS: Fundamentals of L3VPN

![Topology Diagram](https://github.com/tomammon/network-configurations/blob/master/cisco-mpls-l3vpn/cisco-mpls-l3vpn.png)

**Platform: Cisco IOS-XE (CSR1000V 16.5.2)**

### Notes
  * Each LSR is configured with a custom label range to illustrate the locally-significant nature of label advertisements

### Host8
```
Host8> ping 172.16.67.7

84 bytes from 172.16.67.7 icmp_seq=1 ttl=58 time=6.036 ms
84 bytes from 172.16.67.7 icmp_seq=2 ttl=58 time=4.117 ms
84 bytes from 172.16.67.7 icmp_seq=3 ttl=58 time=6.494 ms
^C
Host8> trace 172.16.67.7
trace to 172.16.67.7, 8 hops max, press Ctrl+C to stop
 1   172.16.58.5   1.989 ms  1.724 ms  1.464 ms
 2   10.1.35.3   1.564 ms  1.939 ms  0.953 ms
 3   192.168.13.1   3.942 ms  4.323 ms  4.884 ms
 4   192.168.12.2   4.309 ms  6.470 ms  5.011 ms
 5   10.1.46.4   3.343 ms  2.696 ms  3.192 ms
 6   10.1.46.6   4.926 ms  4.765 ms  4.779 ms
 7   *172.16.67.7   2.791 ms (ICMP type:3, code:3, Destination port unreachable)
```

### CE5
```
CE5#trace 172.16.67.7 source gi0/1
Type escape sequence to abort.
Tracing the route to 172.16.67.7
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.35.3 2 msec 3 msec 3 msec
  2 192.168.13.1 [MPLS: Labels 102/406 Exp 0] 6 msec 6 msec 5 msec
  3 192.168.12.2 [MPLS: Labels 202/406 Exp 0] 5 msec 6 msec 4 msec
  4 10.1.46.4 [AS 6] [MPLS: Label 406 Exp 0] 4 msec 5 msec 5 msec
  5 10.1.46.6 [AS 6] 6 msec 6 msec 6 msec
  6 172.16.67.7 [AS 6] 7 msec 6 msec 4 msec

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

### PE3
```
PE3#sh vrf
  Name                             Default RD            Protocols   Interfaces
  VPN-RED                          3.3.3.3:100           ipv4        Gi3

PE3#show ip bgp vpnv4 vrf VPN-RED
BGP table version is 7, local router ID is 3.3.3.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 3.3.3.3:100 (default for vrf VPN-RED)
 r>   10.1.35.0/24     10.1.35.5                0             0 5 i
 *>i  10.1.46.0/24     4.4.4.4                  0    100      0 6 i
 *>   172.16.58.0/24   10.1.35.5                0             0 5 i
 *>i  172.16.67.0/24   4.4.4.4                  0    100      0 6 i

PE3#show ip bgp vpnv4 vrf VPN-RED 172.16.67.0
BGP routing table entry for 3.3.3.3:100:172.16.67.0/24, version 7
Paths: (1 available, best #1, table VPN-RED)
  Advertised to update-groups:
     1
  Refresh Epoch 1
  6, imported path from 4.4.4.4:100:172.16.67.0/24 (global)
    4.4.4.4 (metric 40) (via default) from 4.4.4.4 (4.4.4.4)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      Extended Community: RT:100:100
      mpls labels in/out nolabel/406
      rx pathid: 0, tx pathid: 0x0

PE3#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 40, type level-2
  Redistributing via isis lab
  Last update from 192.168.13.1 on GigabitEthernet1, 00:27:16 ago
  Routing Descriptor Blocks:
  * 192.168.13.1, from 4.4.4.4, 00:27:16 ago, via GigabitEthernet1
      Route metric is 40, traffic share count is 1

PE3#sh mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
302        102        4.4.4.4/32       0             Gi1        192.168.13.1
        MAC/Encaps=14/18, MRU=1500, Label Stack{102}
        5000000100005000000300008847 00066000
        No output feature configured

PE3#sh ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2
  dflt local label info: global/302 [0x3]
  1 RR source [no flags]
  nexthop 192.168.13.1 GigabitEthernet1 label 102-(local:302)

PE3#sh mpls ldp bindings
  lib entry: 1.1.1.1/32, rev 2
        local binding:  label: 300
        remote binding: lsr: 1.1.1.1:0, label: imp-null
  lib entry: 2.2.2.2/32, rev 4
        local binding:  label: 301
        remote binding: lsr: 1.1.1.1:0, label: 100
  lib entry: 3.3.3.3/32, rev 6
        local binding:  label: imp-null
        remote binding: lsr: 1.1.1.1:0, label: 101
  lib entry: 4.4.4.4/32, rev 8
        local binding:  label: 302
        remote binding: lsr: 1.1.1.1:0, label: 102
  lib entry: 192.168.12.0/24, rev 10
        local binding:  label: 303
        remote binding: lsr: 1.1.1.1:0, label: imp-null
  lib entry: 192.168.13.0/24, rev 12
        local binding:  label: imp-null
        remote binding: lsr: 1.1.1.1:0, label: imp-null
  lib entry: 192.168.14.0/24, rev 17
        no local binding
        remote binding: lsr: 1.1.1.1:0, label: imp-null
  lib entry: 192.168.23.0/24, rev 14
        local binding:  label: imp-null
  lib entry: 192.168.24.0/24, rev 16
        local binding:  label: 304
        remote binding: lsr: 1.1.1.1:0, label: 103

PE3#show ip route vrf VPN-RED 172.16.67.0

Routing Table: VPN-RED
Routing entry for 172.16.67.0/24
  Known via "bgp 105", distance 200, metric 0
  Tag 6, type internal
  Last update from 4.4.4.4 00:33:57 ago
  Routing Descriptor Blocks:
  * 4.4.4.4 (default), from 4.4.4.4, 00:33:57 ago
      Route metric is 0, traffic share count is 1
      AS Hops 1
      Route tag 6
      MPLS label: 406
      MPLS Flags: MPLS Required

PE3#sh ip bgp vpnv4 vrf VPN-RED labels
   Network          Next Hop      In label/Out label
Route Distinguisher: 3.3.3.3:100 (VPN-RED)
   10.1.35.0/24     10.1.35.5       305/nolabel
   10.1.46.0/24     4.4.4.4         nolabel/405
   172.16.58.0/24   10.1.35.5       306/nolabel
   172.16.67.0/24   4.4.4.4         nolabel/406

PE3#sh mpls forwarding-table vrf VPN-RED 172.16.67.0 detail
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
None       406        172.16.67.0/24[V]   \
                                       0             Gi1        192.168.13.1
        MAC/Encaps=14/22, MRU=1496, Label Stack{102 406}
        5000000100005000000300008847 0006600000196000
        VPN route: VPN-RED
        No output feature configured

PE3#sh ip cef vrf VPN-RED 172.16.67.0 deta
172.16.67.0/24, epoch 2, flags [rib defined all labels]
  recursive via 4.4.4.4 label 406
    nexthop 192.168.13.1 GigabitEthernet1 label 102-(local:302)
```

### P1
```
P1#sh vrf
P1#

P1#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 30, type level-2
  Redistributing via isis lab
  Last update from 192.168.12.2 on GigabitEthernet2, 00:39:32 ago
  Routing Descriptor Blocks:
  * 192.168.12.2, from 4.4.4.4, 00:39:32 ago, via GigabitEthernet2
      Route metric is 30, traffic share count is 1

P1#sh mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
102        202        4.4.4.4/32       8665          Gi2        192.168.12.2
        MAC/Encaps=14/18, MRU=1500, Label Stack{202}
        5000000200005000000100018847 000CA000
        No output feature configured

P1#show ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2
  dflt local label info: global/102 [0x3]
  nexthop 192.168.12.2 GigabitEthernet2 label 202-(local:102)

P1#sh mpls ldp bindings
  lib entry: 1.1.1.1/32, rev 2
        local binding:  label: imp-null
        remote binding: lsr: 3.3.3.3:0, label: 300
        remote binding: lsr: 2.2.2.2:0, label: 200
  lib entry: 2.2.2.2/32, rev 4
        local binding:  label: 100
        remote binding: lsr: 3.3.3.3:0, label: 301
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 3.3.3.3/32, rev 6
        local binding:  label: 101
        remote binding: lsr: 3.3.3.3:0, label: imp-null
        remote binding: lsr: 2.2.2.2:0, label: 201
  lib entry: 4.4.4.4/32, rev 8
        local binding:  label: 102
        remote binding: lsr: 3.3.3.3:0, label: 302
        remote binding: lsr: 2.2.2.2:0, label: 202
  lib entry: 192.168.12.0/24, rev 10
        local binding:  label: imp-null
        remote binding: lsr: 3.3.3.3:0, label: 303
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 192.168.13.0/24, rev 12
        local binding:  label: imp-null
        remote binding: lsr: 3.3.3.3:0, label: imp-null
        remote binding: lsr: 2.2.2.2:0, label: 203
  lib entry: 192.168.14.0/24, rev 14
        local binding:  label: imp-null
  lib entry: 192.168.23.0/24, rev 17
        no local binding
        remote binding: lsr: 3.3.3.3:0, label: imp-null
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 192.168.24.0/24, rev 16
        local binding:  label: 103
        remote binding: lsr: 3.3.3.3:0, label: 304
        remote binding: lsr: 2.2.2.2:0, label: imp-null
```

### P2
```
P2#show vrf
P2#

P2#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 20, type level-2
  Redistributing via isis lab
  Last update from 192.168.24.4 on GigabitEthernet2, 00:41:25 ago
  Routing Descriptor Blocks:
  * 192.168.24.4, from 4.4.4.4, 00:41:25 ago, via GigabitEthernet2
      Route metric is 20, traffic share count is 1

P2#show mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
202        Pop Label  4.4.4.4/32       9046          Gi2        192.168.24.4
        MAC/Encaps=14/14, MRU=1504, Label Stack{}
        5000000400015000000200018847
        No output feature configured

P2#show ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2
  dflt local label info: global/202 [0x3]
  nexthop 192.168.24.4 GigabitEthernet2

P2#sh mpls ldp bindings
  lib entry: 1.1.1.1/32, rev 2
        local binding:  label: 200
        remote binding: lsr: 1.1.1.1:0, label: imp-null
        remote binding: lsr: 4.4.4.4:0, label: 400
  lib entry: 2.2.2.2/32, rev 4
        local binding:  label: imp-null
        remote binding: lsr: 1.1.1.1:0, label: 100
        remote binding: lsr: 4.4.4.4:0, label: 401
  lib entry: 3.3.3.3/32, rev 6
        local binding:  label: 201
        remote binding: lsr: 1.1.1.1:0, label: 101
        remote binding: lsr: 4.4.4.4:0, label: 402
  lib entry: 4.4.4.4/32, rev 8
        local binding:  label: 202
        remote binding: lsr: 1.1.1.1:0, label: 102
        remote binding: lsr: 4.4.4.4:0, label: imp-null
  lib entry: 192.168.12.0/24, rev 10
        local binding:  label: imp-null
        remote binding: lsr: 1.1.1.1:0, label: imp-null
        remote binding: lsr: 4.4.4.4:0, label: 403
  lib entry: 192.168.13.0/24, rev 12
        local binding:  label: 203
        remote binding: lsr: 1.1.1.1:0, label: imp-null
        remote binding: lsr: 4.4.4.4:0, label: 404
  lib entry: 192.168.14.0/24, rev 17
        no local binding
        remote binding: lsr: 1.1.1.1:0, label: imp-null
        remote binding: lsr: 4.4.4.4:0, label: imp-null
  lib entry: 192.168.23.0/24, rev 14
        local binding:  label: imp-null
  lib entry: 192.168.24.0/24, rev 16
        local binding:  label: imp-null
        remote binding: lsr: 1.1.1.1:0, label: 103
        remote binding: lsr: 4.4.4.4:0, label: imp-null
```

### PE4
```

PE4#sh vrf
  Name                             Default RD            Protocols   Interfaces
  VPN-RED                          4.4.4.4:100           ipv4        Gi1

PE4#show ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "connected", distance 0, metric 0 (connected, via interface)
  Routing Descriptor Blocks:
  * directly connected, via Loopback0
      Route metric is 0, traffic share count is 1

PE4#show mpls forwa
PE4#show mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
None       No Label   4.4.4.4/32       0
        MAC/Encaps=0/0, MRU=0, Label Stack{}
        No output feature configured

PE4#show ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2, flags [attached, connected, receive, local, source eligible]
  Interface source: Loopback0 flags: local, source eligible flags3: none
  receive for Loopback0

PE4#sh mpls ldp bindings
  lib entry: 1.1.1.1/32, rev 2
        local binding:  label: 400
        remote binding: lsr: 2.2.2.2:0, label: 200
  lib entry: 2.2.2.2/32, rev 4
        local binding:  label: 401
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 3.3.3.3/32, rev 6
        local binding:  label: 402
        remote binding: lsr: 2.2.2.2:0, label: 201
  lib entry: 4.4.4.4/32, rev 8
        local binding:  label: imp-null
        remote binding: lsr: 2.2.2.2:0, label: 202
  lib entry: 192.168.12.0/24, rev 10
        local binding:  label: 403
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 192.168.13.0/24, rev 12
        local binding:  label: 404
        remote binding: lsr: 2.2.2.2:0, label: 203
  lib entry: 192.168.14.0/24, rev 14
        local binding:  label: imp-null
  lib entry: 192.168.23.0/24, rev 17
        no local binding
        remote binding: lsr: 2.2.2.2:0, label: imp-null
  lib entry: 192.168.24.0/24, rev 16
        local binding:  label: imp-null
        remote binding: lsr: 2.2.2.2:0, label: imp-null

PE4#show ip bgp vpnv4 vrf VPN-RED 172.16.67.0
BGP routing table entry for 4.4.4.4:100:172.16.67.0/24, version 3
Paths: (1 available, best #1, table VPN-RED)
  Advertised to update-groups:
     2
  Refresh Epoch 1
  6
    10.1.46.6 (via vrf VPN-RED) from 10.1.46.6 (6.6.6.6)
      Origin IGP, metric 0, localpref 100, valid, external, best
      Extended Community: RT:100:100
      mpls labels in/out 406/nolabel
      rx pathid: 0, tx pathid: 0x0

PE4#show ip route vrf VPN-RED 172.16.67.0

Routing Table: VPN-RED
Routing entry for 172.16.67.0/24
  Known via "bgp 105", distance 20, metric 0
  Tag 6, type external
  Last update from 10.1.46.6 00:42:53 ago
  Routing Descriptor Blocks:
  * 10.1.46.6, from 10.1.46.6, 00:42:53 ago
      Route metric is 0, traffic share count is 1
      AS Hops 1
      Route tag 6
      MPLS label: none

PE4#sh ip bgp vpnv4 vrf VPN-RED labels
   Network          Next Hop      In label/Out label
Route Distinguisher: 4.4.4.4:100 (VPN-RED)
   10.1.35.0/24     3.3.3.3         nolabel/305
   10.1.46.0/24     10.1.46.6       405/nolabel
   172.16.58.0/24   3.3.3.3         nolabel/306
   172.16.67.0/24   10.1.46.6       406/nolabel

PE4#show mpls forwarding-table vrf VPN-RED 172.16.67.0 detail
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
406        No Label   172.16.67.0/24[V]   \
                                       3708          Gi1        10.1.46.6
        MAC/Encaps=14/14, MRU=1504, Label Stack{}
        5000000600005000000400000800
        VPN route: VPN-RED
        No output feature configured

PE4#show ip cef vrf VPN-RED 172.16.67.0 deta
172.16.67.0/24, epoch 2, flags [rib only nolabel, rib defined all labels]
  dflt local label info: other/406 [0x2]
  recursive via 10.1.46.6
    attached to GigabitEthernet1
```

### CE6
```
CE6#show ip bgp 172.16.67.0
BGP routing table entry for 172.16.67.0/24, version 3
Paths: (1 available, best #1, table default)
  Advertised to update-groups:
     1
  Refresh Epoch 1
  Local
    0.0.0.0 from 0.0.0.0 (6.6.6.6)
      Origin IGP, metric 0, localpref 100, weight 32768, valid, sourced, local, best
      rx pathid: 0, tx pathid: 0x0

CE6#sh ip route 172.16.67.0
Routing entry for 172.16.67.0/24
  Known via "connected", distance 0, metric 0 (connected, via interface)
  Advertised by bgp 6
  Routing Descriptor Blocks:
  * directly connected, via GigabitEthernet0/1
      Route metric is 0, traffic share count is 1

CE6#sh ip cef 172.16.67.0
172.16.67.0/32
  receive for GigabitEthernet0/1
```
