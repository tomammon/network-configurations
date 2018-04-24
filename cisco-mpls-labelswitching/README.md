## MPLS: Fundamentals of Label Switching

![Topology Diagram](https://github.com/tomammon/network-configurations/blob/master/cisco-mpls-labelswitching/cisco-mpls-labelswitching.png)

**Platform: Cisco IOS-XE**

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
  Last update from 10.1.35.3 00:38:58 ago
  Routing Descriptor Blocks:
  * 10.1.35.3, from 10.1.35.3, 00:38:58 ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 105
      MPLS label: none

CE5#sh ip cef 172.16.67.0 detail
172.16.67.0/24, epoch 0, flags [rib only nolabel, rib defined all labels]
  recursive via 10.1.35.3
    attached to GigabitEthernet0/0

CE5#sh ip route 10.1.35.3
Routing entry for 10.1.35.0/24
  Known via "connected", distance 0, metric 0 (connected, via interface)
  Advertised by bgp 5
  Routing Descriptor Blocks:
  * directly connected, via GigabitEthernet0/0
      Route metric is 0, traffic share count is 1

CE5#show ip cef 10.1.35.3
10.1.35.3/32
  attached to GigabitEthernet0/0
```
