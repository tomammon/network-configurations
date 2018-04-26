## MPLS: Fundamentals of Label Switching with LDP

![Topology Diagram](https://github.com/tomammon/network-configurations/blob/master/cisco-mpls-labelswitching/cisco-mpls-labelswitching.png)

**Platform: Cisco IOS-XE (CSR1000V 16.5.2)**

### Notes
  * Each LSR is configured with a custom label range to illustrate the locally-significant nature of label advertisements 

### PE3
```
PE3#ping 4.4.4.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 4.4.4.4, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms

PE3#trace 4.4.4.4
Type escape sequence to abort.
Tracing the route to 4.4.4.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.168.13.1 [MPLS: Label 102 Exp 0] 4 msec 3 msec 2 msec
  2 192.168.12.2 [MPLS: Label 201 Exp 0] 2 msec 2 msec 2 msec
  3 192.168.24.4 2 msec 4 msec *

PE3#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 40, type level-2
  Redistributing via isis lab
  Last update from 192.168.13.1 on GigabitEthernet1, 00:04:13 ago
  Routing Descriptor Blocks:
  * 192.168.13.1, from 4.4.4.4, 00:04:13 ago, via GigabitEthernet1
      Route metric is 40, traffic share count is 1

PE3#sh ip cef 4.4.4.4
4.4.4.4/32
  nexthop 192.168.13.1 GigabitEthernet1 label 102-(local:302)

PE3#sh mpls forwarding-table 4.4.4.4 detail
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
302        102        4.4.4.4/32       0             Gi1        192.168.13.1
        MAC/Encaps=14/18, MRU=1500, Label Stack{102}
        5000000100005000000300008847 00066000
        No output feature configured

PE3#show mpls ldp bindings detail
  lib entry: 1.1.1.1/32, rev 2, chkpt: none
        local binding:  label: 300 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
  lib entry: 2.2.2.2/32, rev 4, chkpt: none
        local binding:  label: 301 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 100 checkpointed
  lib entry: 3.3.3.3/32, rev 6, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 101 checkpointed
  lib entry: 4.4.4.4/32, rev 8, chkpt: none
        local binding:  label: 302 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 102 checkpointed
  lib entry: 192.168.12.0/24, rev 10, chkpt: none
        local binding:  label: 303 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
  lib entry: 192.168.13.0/24, rev 12, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
  lib entry: 192.168.14.0/24, rev 17, chkpt: none
        no local binding
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
  lib entry: 192.168.23.0/24, rev 14, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0
  lib entry: 192.168.24.0/24, rev 16, chkpt: none
        local binding:  label: 304 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 103 checkpointed
```

### P1
```
P1#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 30, type level-2
  Redistributing via isis lab
  Last update from 192.168.12.2 on GigabitEthernet2, 00:08:45 ago
  Routing Descriptor Blocks:
  * 192.168.12.2, from 4.4.4.4, 00:08:45 ago, via GigabitEthernet2
      Route metric is 30, traffic share count is 1
P1#
P1#sh ip cef 4.4.4.4
4.4.4.4/32
  nexthop 192.168.12.2 GigabitEthernet2 label 201-(local:102)

P1#show mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
102        201        4.4.4.4/32       2800          Gi2        192.168.12.2
        MAC/Encaps=14/18, MRU=1500, Label Stack{201}
        5000000200005000000100018847 000C9000
        No output feature configured

P1#sh mpls ldp bindings detail
  lib entry: 1.1.1.1/32, rev 2, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: 300 checkpointed
        remote binding: lsr: 2.2.2.2:0, label: 200 checkpointed
  lib entry: 2.2.2.2/32, rev 4, chkpt: none
        local binding:  label: 100 (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: 301 checkpointed
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 3.3.3.3/32, rev 6, chkpt: none
        local binding:  label: 101 (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: imp-null checkpointed
        remote binding: lsr: 2.2.2.2:0, label: 202 checkpointed
  lib entry: 4.4.4.4/32, rev 8, chkpt: none
        local binding:  label: 102 (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: 302 checkpointed
        remote binding: lsr: 2.2.2.2:0, label: 201 checkpointed
  lib entry: 192.168.12.0/24, rev 10, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: 303 checkpointed
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 192.168.13.0/24, rev 12, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: imp-null checkpointed
        remote binding: lsr: 2.2.2.2:0, label: 203 checkpointed
  lib entry: 192.168.14.0/24, rev 14, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
  lib entry: 192.168.23.0/24, rev 17, chkpt: none
        no local binding
        remote binding: lsr: 3.3.3.3:0, label: imp-null checkpointed
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 192.168.24.0/24, rev 16, chkpt: none
        local binding:  label: 103 (owner LDP)
          Advertised to:
          3.3.3.3:0              2.2.2.2:0
        remote binding: lsr: 3.3.3.3:0, label: 304 checkpointed
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
```

### P2
```
P2#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 20, type level-2
  Redistributing via isis lab
  Last update from 192.168.24.4 on GigabitEthernet2, 00:15:42 ago
  Routing Descriptor Blocks:
  * 192.168.24.4, from 4.4.4.4, 00:15:42 ago, via GigabitEthernet2
      Route metric is 20, traffic share count is 1

P2#sh ip cef 4.4.4.4
4.4.4.4/32
  nexthop 192.168.24.4 GigabitEthernet2

P2#show mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
201        Pop Label  4.4.4.4/32       3630          Gi2        192.168.24.4
        MAC/Encaps=14/14, MRU=1504, Label Stack{}
        5000000400015000000200018847
        No output feature configured

P2#show mpls ldp bindings detail
  lib entry: 1.1.1.1/32, rev 10, chkpt: none
        local binding:  label: 200 (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
        remote binding: lsr: 4.4.4.4:0, label: 400 checkpointed
  lib entry: 2.2.2.2/32, rev 2, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: 100 checkpointed
        remote binding: lsr: 4.4.4.4:0, label: 401 checkpointed
  lib entry: 3.3.3.3/32, rev 14, chkpt: none
        local binding:  label: 202 (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: 101 checkpointed
        remote binding: lsr: 4.4.4.4:0, label: 402 checkpointed
  lib entry: 4.4.4.4/32, rev 12, chkpt: none
        local binding:  label: 201 (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: 102 checkpointed
        remote binding: lsr: 4.4.4.4:0, label: imp-null checkpointed
  lib entry: 192.168.12.0/24, rev 4, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
        remote binding: lsr: 4.4.4.4:0, label: 403 checkpointed
  lib entry: 192.168.13.0/24, rev 16, chkpt: none
        local binding:  label: 203 (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
        remote binding: lsr: 4.4.4.4:0, label: 404 checkpointed
  lib entry: 192.168.14.0/24, rev 17, chkpt: none
        no local binding
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
        remote binding: lsr: 4.4.4.4:0, label: imp-null checkpointed
  lib entry: 192.168.23.0/24, rev 8, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
  lib entry: 192.168.24.0/24, rev 6, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0              4.4.4.4:0
        remote binding: lsr: 1.1.1.1:0, label: 103 checkpointed
        remote binding: lsr: 4.4.4.4:0, label: imp-null checkpointed
```

### PE4
```
PE4#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "connected", distance 0, metric 0 (connected, via interface)
  Routing Descriptor Blocks:
  * directly connected, via Loopback0
      Route metric is 0, traffic share count is 1

PE4#sh ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2, flags [attached, connected, receive, local, source eligible]
  Interface source: Loopback0 flags: local, source eligible flags3: none
  receive for Loopback0

PE4#sh mpls forwarding-table 4.4.4.4 deta
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
None       No Label   4.4.4.4/32       0
        MAC/Encaps=0/0, MRU=0, Label Stack{}
        No output feature configured

PE4#show mpls ldp bindings deta
  lib entry: 1.1.1.1/32, rev 2, chkpt: none
        local binding:  label: 400 (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: 200 checkpointed
  lib entry: 2.2.2.2/32, rev 4, chkpt: none
        local binding:  label: 401 (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 3.3.3.3/32, rev 6, chkpt: none
        local binding:  label: 402 (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: 202 checkpointed
  lib entry: 4.4.4.4/32, rev 8, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: 201 checkpointed
  lib entry: 192.168.12.0/24, rev 10, chkpt: none
        local binding:  label: 403 (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 192.168.13.0/24, rev 12, chkpt: none
        local binding:  label: 404 (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: 203 checkpointed
  lib entry: 192.168.14.0/24, rev 14, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          2.2.2.2:0
  lib entry: 192.168.23.0/24, rev 17, chkpt: none
        no local binding
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
  lib entry: 192.168.24.0/24, rev 16, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          2.2.2.2:0
        remote binding: lsr: 2.2.2.2:0, label: imp-null checkpointed
```
