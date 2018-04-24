## MPLS: Fundamentals of Label Switching

![Topology Diagram](https://github.com/tomammon/network-configurations/blob/master/cisco-mpls-labelswitching/cisco-mpls-labelswitching.png)

**Platform: Cisco IOS-XE**

### PE3
```
PE3#trace 4.4.4.4 so 3.3.3.3
Type escape sequence to abort.
Tracing the route to 4.4.4.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.168.13.1 [MPLS: Label 18 Exp 0] 4 msec 3 msec 2 msec
  2 192.168.12.2 [MPLS: Label 18 Exp 0] 2 msec 4 msec 2 msec
  3 192.168.24.4 2 msec 2 msec *

PE3#sh ip route 4.4.4.4
Routing entry for 4.4.4.4/32
  Known via "isis", distance 115, metric 40, type level-2
  Redistributing via isis lab
  Last update from 192.168.13.1 on GigabitEthernet1, 00:45:10 ago
  Routing Descriptor Blocks:
  * 192.168.13.1, from 4.4.4.4, 00:45:10 ago, via GigabitEthernet1
      Route metric is 40, traffic share count is 1

PE3#sh ip cef 4.4.4.4 deta
4.4.4.4/32, epoch 2
  dflt local label info: global/18 [0x3]
  1 RR source [no flags]
  nexthop 192.168.13.1 GigabitEthernet1 label 18-(local:18)


PE3#sh mpls forwarding-table
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop
Label      Label      or Tunnel Id     Switched      interface
16         Pop Label  1.1.1.1/32       0             Gi1        192.168.13.1
17         16         2.2.2.2/32       0             Gi1        192.168.13.1
18         18         4.4.4.4/32       0             Gi1        192.168.13.1
19         Pop Label  192.168.12.0/24  0             Gi1        192.168.13.1
20         19         192.168.24.0/24  0             Gi1        192.168.13.1
21         No Label   10.1.35.0/24[V]  0             aggregate/VPN-RED
22         No Label   172.16.58.0/24[V]   \
                                       2424          Gi3        10.1.35.5

PE3#show mpls ldp neighbor
    Peer LDP Ident: 1.1.1.1:0; Local LDP Ident 3.3.3.3:0
        TCP connection: 1.1.1.1.646 - 3.3.3.3.15242
        State: Oper; Msgs sent/rcvd: 64/65; Downstream
        Up time: 00:46:52
        LDP discovery sources:
          GigabitEthernet1, Src IP addr: 192.168.13.1
        Addresses bound to peer LDP Ident:
          1.1.1.1         192.168.13.1    192.168.12.1    192.168.14.1

PE3#show mpls ldp disco
 Local LDP Identifier:
    3.3.3.3:0
    Discovery Sources:
    Interfaces:
        GigabitEthernet1 (ldp): xmit/recv
            LDP Id: 1.1.1.1:0

PE3#sh mpls ldp bindings detail
  lib entry: 1.1.1.1/32, rev 2, chkpt: none
        local binding:  label: 16 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: imp-null checkpointed
  lib entry: 2.2.2.2/32, rev 4, chkpt: none
        local binding:  label: 17 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 16 checkpointed
  lib entry: 3.3.3.3/32, rev 6, chkpt: none
        local binding:  label: imp-null (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 17 checkpointed
  lib entry: 4.4.4.4/32, rev 8, chkpt: none
        local binding:  label: 18 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 18 checkpointed
  lib entry: 192.168.12.0/24, rev 10, chkpt: none
        local binding:  label: 19 (owner LDP)
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
        local binding:  label: 20 (owner LDP)
          Advertised to:
          1.1.1.1:0
        remote binding: lsr: 1.1.1.1:0, label: 19 checkpointed

```
