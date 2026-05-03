### Построение Underlay сети (IS-IS)

### Цели/Задачи
1) Настроить iBGP в Underlay сети, для IP связанности между всеми сетевыми устройствами. 
2) Зафиксировать в документации - план работы, адресное пространство, схему сети, конфигурацию устройств.
3) Убедиться в наличии IP связанности между устройствами в BGP домене

### Реализация
Схема сети
![схема сети](scheme_3.png)

### ip план

| Устройство | Интерфейс | IP-адрес       | Loopback IP    | Дескрипшен                       |
|------------|-----------|----------------|----------------|----------------------------------|
| leaf-01    | eth7      | 10.10.10.0/31  | 10.0.0.1/32    | spine-01_et1                     |
| leaf-01    | eth8      | 10.10.10.2/31  | 10.0.0.1/32    | spine-02_et1                     |
| leaf-02    | eth7      | 10.10.10.4/31  | 10.0.0.2/32    | spine-01_et2                     |
| leaf-02    | eth8      | 10.10.10.6/31  | 10.0.0.2/32    | spine-02_et2                     |
| leaf-03    | eth7      | 10.10.10.8/31  | 10.0.0.3/32    | spine-01_et3                     |
| leaf-03    | eth8      | 10.10.10.10/31 | 10.0.0.3/32    | spine-02_et3                     |
| spine-01   | eth1      | 10.10.10.1/31  | 10.0.0.4/32    | leaf-01_et7                      |
| spine-01   | eth2      | 10.10.10.5/31  | 10.0.0.4/32    | leaf-02_et7                      |
| spine-01   | eth3      | 10.10.10.9/31  | 10.0.0.4/32    | leaf-03_et7                      |
| spine-02   | eth1      | 10.10.10.3/31  | 10.0.0.5/32    | leaf-01_et8                      |
| spine-02   | eth2      | 10.10.10.7/31  | 10.0.0.5/32    | leaf-02_et8                      |
| spine-02   | eth3      | 10.10.10.11/31 | 10.0.0.5/32    | leaf-03_et8                      |


### Конфигурации
<details>
<summary><b>leaf-01</b> (нажмите, чтобы раскрыть)</summary>

```cisco
leaf-01#sh run sec bgp
router bgp 65000
   router-id 10.0.0.1
   maximum-paths 4 ecmp 4
   neighbor SPINES peer group
   neighbor SPINES remote-as 65000
   neighbor SPINES bfd
   neighbor SPINES route-reflector-client
   neighbor SPINES timers 3 9
   neighbor SPINES send-community extended
   neighbor 10.10.10.1 peer group SPINES
   neighbor 10.10.10.1 description spine-01
   neighbor 10.10.10.3 peer group SPINES
   neighbor 10.10.10.3 description spine-02
   !
   address-family ipv4
      neighbor SPINES activate
      redistribute connected route-map RM_RED_Lo
leaf-01#sh run sec route-map
route-map RM_RED_Lo permit 10
   set origin igp
router bgp 65000
   address-family ipv4
      redistribute connected route-map RM_RED_Lo
```

</details>

<details>
<summary><b>leaf-02</b> (нажмите, чтобы раскрыть)</summary>

```cisco
leaf-02#sh run sec bgp
router bgp 65000
   router-id 10.0.0.2
   maximum-paths 4 ecmp 4
   neighbor SPINES peer group
   neighbor SPINES remote-as 65000
   neighbor SPINES bfd
   neighbor SPINES route-reflector-client
   neighbor SPINES timers 3 9
   neighbor SPINES send-community extended
   neighbor 10.10.10.5 peer group SPINES
   neighbor 10.10.10.5 description spine-01
   neighbor 10.10.10.7 peer group SPINES
   neighbor 10.10.10.7 description spine-02
   !
   address-family ipv4
      neighbor SPINES activate
      redistribute connected route-map RM_RED_Lo
leaf-02#sh run sec route-map
route-map RM_RED_Lo permit 10
   set origin igp
router bgp 65000
   address-family ipv4
      redistribute connected route-map RM_RED_Lo
```

</details>

<details>
<summary><b>leaf-03</b> (нажмите, чтобы раскрыть)</summary>

```cisco
leaf-03#sh run sec bgp
router bgp 65000
   router-id 10.0.0.3
   maximum-paths 4 ecmp 4
   neighbor SPINES peer group
   neighbor SPINES remote-as 65000
   neighbor SPINES bfd
   neighbor SPINES route-reflector-client
   neighbor SPINES timers 3 9
   neighbor SPINES send-community extended
   neighbor 10.10.10.9 peer group SPINES
   neighbor 10.10.10.9 description spine-01
   neighbor 10.10.10.11 peer group SPINES
   neighbor 10.10.10.11 description spine-02
   !
   address-family ipv4
      neighbor SPINES activate
      redistribute connected route-map RM_RED_Lo
leaf-03#sh run sec route-map
route-map RM_RED_Lo permit 10
   set origin igp
router bgp 65000
   address-family ipv4
      redistribute connected route-map RM_RED_Lo
```

</details>

<details>
<summary><b>spine-01</b> (нажмите, чтобы раскрыть)</summary>

```cisco
spine-01#sh run sec bgp
router bgp 65000
   router-id 10.0.0.4
   maximum-paths 4 ecmp 4
   neighbor LEAFS peer group
   neighbor LEAFS remote-as 65000
   neighbor LEAFS bfd
   neighbor LEAFS route-reflector-client
   neighbor LEAFS timers 3 9
   neighbor LEAFS send-community extended
   neighbor 10.10.10.0 peer group LEAFS
   neighbor 10.10.10.0 description leaf-01
   neighbor 10.10.10.4 peer group LEAFS
   neighbor 10.10.10.4 description leaf-02
   neighbor 10.10.10.8 peer group LEAFS
   neighbor 10.10.10.8 description leaf-03
   !
   address-family ipv4
      neighbor LEAFS activate
      neighbor LEAFS next-hop-self
      redistribute connected route-map RM_RED_Lo
spine-01#sh run sec route-map
route-map RM_RED_Lo permit 10
   set origin igp
router bgp 65000
   address-family ipv4
      redistribute connected route-map RM_RED_Lo
```

</details>

<details>
<summary><b>spine-02</b> (нажмите, чтобы раскрыть)</summary>

```cisco
spine-02#sh run sec bgp
router bgp 65000
   router-id 10.0.0.5
   maximum-paths 4 ecmp 4
   neighbor LEAFS peer group
   neighbor LEAFS remote-as 65000
   neighbor LEAFS bfd
   neighbor LEAFS route-reflector-client
   neighbor LEAFS timers 3 9
   neighbor LEAFS send-community extended
   neighbor 10.10.10.2 peer group LEAFS
   neighbor 10.10.10.2 description leaf-01
   neighbor 10.10.10.6 peer group LEAFS
   neighbor 10.10.10.6 description leaf-02
   neighbor 10.10.10.10 peer group LEAFS
   neighbor 10.10.10.10 description leaf-03
   !
   address-family ipv4
      neighbor LEAFS activate
      neighbor LEAFS next-hop-self
      redistribute connected route-map RM_RED_Lo
spine-02#sh run sec route-map
route-map RM_RED_Lo permit 10
   set origin incomplete
router bgp 65000
   address-family ipv4
      redistribute connected route-map RM_RED_Lo
```

</details>

### Проверка соседства/распространения маршрутов
```cisco
leaf-01#sh ip bgp su
BGP summary information for VRF default
Router identifier 10.0.0.1, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Description              Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  spine-01                 10.10.10.1 4 65000           1828      1826    0    0 00:18:09 Estab   8      8
  spine-02                 10.10.10.3 4 65000           1833      1835    0    0 00:22:05 Estab   8      8
leaf-01#sh ip bgp
BGP routing table information for VRF default
Router identifier 10.0.0.1, local AS number 65000
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >      10.0.0.1/32            -                     -       -          -       0       i
 * >Ec    10.0.0.2/32            10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.5
 *  ec    10.0.0.2/32            10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.4
 * >Ec    10.0.0.3/32            10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.5
 *  ec    10.0.0.3/32            10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.4
 * >      10.0.0.4/32            10.10.10.1            0       -          100     0       i
 * >      10.0.0.5/32            10.10.10.3            0       -          100     0       ?
 * >      10.10.10.0/31          -                     -       -          -       0       i
 *        10.10.10.0/31          10.10.10.1            0       -          100     0       i
 * >      10.10.10.2/31          -                     -       -          -       0       i
 *        10.10.10.2/31          10.10.10.3            0       -          100     0       ?
 * >Ec    10.10.10.4/31          10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.5
 *  ec    10.10.10.4/31          10.10.10.1            0       -          100     0       i
 * >      10.10.10.6/31          10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.4
 *        10.10.10.6/31          10.10.10.3            0       -          100     0       ?
 * >Ec    10.10.10.8/31          10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.5
 *  ec    10.10.10.8/31          10.10.10.1            0       -          100     0       i
 * >      10.10.10.10/31         10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.4
 *        10.10.10.10/31         10.10.10.3            0       -          100     0       ?
leaf-01#ping 10.10.10.6
PING 10.10.10.6 (10.10.10.6) 72(100) bytes of data.
80 bytes from 10.10.10.6: icmp_seq=1 ttl=63 time=2.72 ms
80 bytes from 10.10.10.6: icmp_seq=2 ttl=63 time=1.19 ms
80 bytes from 10.10.10.6: icmp_seq=3 ttl=63 time=1.20 ms
80 bytes from 10.10.10.6: icmp_seq=4 ttl=63 time=1.27 ms
80 bytes from 10.10.10.6: icmp_seq=5 ttl=63 time=1.06 ms

--- 10.10.10.6 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 11ms
rtt min/avg/max/mdev = 1.058/1.486/2.715/0.618 ms, ipg/ewma 2.762/2.077 ms
leaf-01#ping 10.0.0.3
PING 10.0.0.3 (10.0.0.3) 72(100) bytes of data.
80 bytes from 10.0.0.3: icmp_seq=1 ttl=63 time=2.70 ms
80 bytes from 10.0.0.3: icmp_seq=2 ttl=63 time=1.24 ms
80 bytes from 10.0.0.3: icmp_seq=3 ttl=63 time=1.19 ms
80 bytes from 10.0.0.3: icmp_seq=4 ttl=63 time=1.31 ms
80 bytes from 10.0.0.3: icmp_seq=5 ttl=63 time=1.24 ms

--- 10.0.0.3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 10ms
rtt min/avg/max/mdev = 1.191/1.534/2.703/0.585 ms, ipg/ewma 2.590/2.099 ms
leaf-02#sh ip bgp su
BGP summary information for VRF default
Router identifier 10.0.0.2, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Description              Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  spine-01                 10.10.10.5 4 65000           1750      1734    0    0 00:18:48 Estab   9      9
  spine-02                 10.10.10.7 4 65000           1744      1748    0    0 00:22:38 Estab   9      9
leaf-02#sh ip bgp
BGP routing table information for VRF default
Router identifier 10.0.0.2, local AS number 65000
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >Ec    10.0.0.1/32            10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.5
 *  ec    10.0.0.1/32            10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.4
 * >      10.0.0.2/32            -                     -       -          -       0       i
 * >Ec    10.0.0.3/32            10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.5
 *  ec    10.0.0.3/32            10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.4
 * >Ec    10.0.0.4/32            10.10.10.5            0       -          100     0       i
 *  ec    10.0.0.4/32            10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.4 C-LST: 10.0.0.5 10.0.0.1
 * >Ec    10.0.0.5/32            10.10.10.7            0       -          100     0       ?
 *  ec    10.0.0.5/32            10.10.10.5            0       -          100     0       ? Or-ID: 10.0.0.5 C-LST: 10.0.0.4 10.0.0.1
 * >Ec    10.10.10.0/31          10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.5
 *  ec    10.10.10.0/31          10.10.10.5            0       -          100     0       i
 * >      10.10.10.2/31          10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.4
 *        10.10.10.2/31          10.10.10.7            0       -          100     0       ?
 * >      10.10.10.4/31          -                     -       -          -       0       i
 *        10.10.10.4/31          10.10.10.5            0       -          100     0       i
 * >      10.10.10.6/31          -                     -       -          -       0       i
 *        10.10.10.6/31          10.10.10.7            0       -          100     0       ?
 * >Ec    10.10.10.8/31          10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.5
 *  ec    10.10.10.8/31          10.10.10.5            0       -          100     0       i
 * >      10.10.10.10/31         10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.4
 *        10.10.10.10/31         10.10.10.7            0       -          100     0       ?
leaf-02#ping 10.10.10.8
PING 10.10.10.8 (10.10.10.8) 72(100) bytes of data.
80 bytes from 10.10.10.8: icmp_seq=1 ttl=63 time=2.23 ms
80 bytes from 10.10.10.8: icmp_seq=2 ttl=63 time=1.50 ms
80 bytes from 10.10.10.8: icmp_seq=3 ttl=63 time=1.27 ms
80 bytes from 10.10.10.8: icmp_seq=4 ttl=63 time=1.20 ms
80 bytes from 10.10.10.8: icmp_seq=5 ttl=63 time=1.19 ms

--- 10.10.10.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 10ms
rtt min/avg/max/mdev = 1.188/1.477/2.231/0.393 ms, ipg/ewma 2.410/1.834 ms
leaf-02#ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 72(100) bytes of data.
80 bytes from 10.0.0.1: icmp_seq=1 ttl=63 time=2.47 ms
80 bytes from 10.0.0.1: icmp_seq=2 ttl=63 time=1.26 ms
80 bytes from 10.0.0.1: icmp_seq=3 ttl=63 time=1.32 ms
80 bytes from 10.0.0.1: icmp_seq=4 ttl=63 time=2.08 ms
80 bytes from 10.0.0.1: icmp_seq=5 ttl=63 time=1.17 ms

--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 12ms
rtt min/avg/max/mdev = 1.170/1.660/2.473/0.520 ms, ipg/ewma 2.901/2.055 ms
leaf-03#sh ip bgp su
BGP summary information for VRF default
Router identifier 10.0.0.3, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Description              Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  spine-01                 10.10.10.9  4 65000           1753      1743    0    0 00:19:31 Estab   9      9
  spine-02                 10.10.10.11 4 65000           1749      1744    0    0 00:23:16 Estab   9      9
leaf-03#sh ip bgp
BGP routing table information for VRF default
Router identifier 10.0.0.3, local AS number 65000
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >Ec    10.0.0.1/32            10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.5
 *  ec    10.0.0.1/32            10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.4
 * >Ec    10.0.0.2/32            10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.5
 *  ec    10.0.0.2/32            10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.4
 * >      10.0.0.3/32            -                     -       -          -       0       i
 * >Ec    10.0.0.4/32            10.10.10.9            0       -          100     0       i
 *  ec    10.0.0.4/32            10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.4 C-LST: 10.0.0.5 10.0.0.1
 * >Ec    10.0.0.5/32            10.10.10.11           0       -          100     0       ?
 *  ec    10.0.0.5/32            10.10.10.9            0       -          100     0       ? Or-ID: 10.0.0.5 C-LST: 10.0.0.4 10.0.0.1
 * >Ec    10.10.10.0/31          10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.5
 *  ec    10.10.10.0/31          10.10.10.9            0       -          100     0       i
 * >      10.10.10.2/31          10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.4
 *        10.10.10.2/31          10.10.10.11           0       -          100     0       ?
 * >Ec    10.10.10.4/31          10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.5
 *  ec    10.10.10.4/31          10.10.10.9            0       -          100     0       i
 * >      10.10.10.6/31          10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.4
 *        10.10.10.6/31          10.10.10.11           0       -          100     0       ?
 * >      10.10.10.8/31          -                     -       -          -       0       i
 *        10.10.10.8/31          10.10.10.9            0       -          100     0       i
 * >      10.10.10.10/31         -                     -       -          -       0       i
 *        10.10.10.10/31         10.10.10.11           0       -          100     0       ?
leaf-03#ping 10.0.0.2
PING 10.0.0.2 (10.0.0.2) 72(100) bytes of data.
80 bytes from 10.0.0.2: icmp_seq=1 ttl=63 time=2.22 ms
80 bytes from 10.0.0.2: icmp_seq=2 ttl=63 time=1.23 ms
80 bytes from 10.0.0.2: icmp_seq=3 ttl=63 time=1.16 ms
80 bytes from 10.0.0.2: icmp_seq=4 ttl=63 time=1.12 ms
80 bytes from 10.0.0.2: icmp_seq=5 ttl=63 time=1.22 ms

--- 10.0.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 9ms
rtt min/avg/max/mdev = 1.123/1.392/2.219/0.415 ms, ipg/ewma 2.228/1.791 ms
leaf-03#ping 10.10.10.0
PING 10.10.10.0 (10.10.10.0) 72(100) bytes of data.
80 bytes from 10.10.10.0: icmp_seq=1 ttl=63 time=2.46 ms
80 bytes from 10.10.10.0: icmp_seq=2 ttl=63 time=1.23 ms
80 bytes from 10.10.10.0: icmp_seq=3 ttl=63 time=1.19 ms
80 bytes from 10.10.10.0: icmp_seq=4 ttl=63 time=1.26 ms
80 bytes from 10.10.10.0: icmp_seq=5 ttl=63 time=1.17 ms

--- 10.10.10.0 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 9ms
rtt min/avg/max/mdev = 1.171/1.459/2.455/0.498 ms, ipg/ewma 2.306/1.939 ms

spine-01#sh ip bgp su
BGP summary information for VRF default
Router identifier 10.0.0.4, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Description              Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  leaf-01                  10.10.10.0 4 65000            570       572    0    0 00:20:48 Estab   8      8
  leaf-02                  10.10.10.4 4 65000            570       571    0    0 00:20:48 Estab   8      8
  leaf-03                  10.10.10.8 4 65000            564       570    0    0 00:20:48 Estab   8      8
spine-01#sh ip bgp
BGP routing table information for VRF default
Router identifier 10.0.0.4, local AS number 65000
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >      10.0.0.1/32            10.10.10.0            0       -          100     0       i
 *  E     10.0.0.1/32            10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.2 10.0.0.5
 *  e     10.0.0.1/32            10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.3 10.0.0.5
 * >      10.0.0.2/32            10.10.10.4            0       -          100     0       i
 *  E     10.0.0.2/32            10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.1 10.0.0.5
 *  e     10.0.0.2/32            10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.3 10.0.0.5
 * >      10.0.0.3/32            10.10.10.8            0       -          100     0       i
 *  E     10.0.0.3/32            10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.1 10.0.0.5
 *  e     10.0.0.3/32            10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.2 10.0.0.5
 * >      10.0.0.4/32            -                     -       -          -       0       i
 * >Ec    10.0.0.5/32            10.10.10.3            0       -          100     0       ? Or-ID: 10.0.0.5 C-LST: 10.0.0.1
 *  ec    10.0.0.5/32            10.10.10.7            0       -          100     0       ? Or-ID: 10.0.0.5 C-LST: 10.0.0.2
 *  ec    10.0.0.5/32            10.10.10.11           0       -          100     0       ? Or-ID: 10.0.0.5 C-LST: 10.0.0.3
 * >      10.10.10.0/31          -                     -       -          -       0       i
 *        10.10.10.0/31          10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.2 10.0.0.5
 *        10.10.10.0/31          10.10.10.0            0       -          100     0       i
 *        10.10.10.0/31          10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.3 10.0.0.5
 * >      10.10.10.2/31          10.10.10.0            0       -          100     0       i
 * >      10.10.10.4/31          -                     -       -          -       0       i
 *        10.10.10.4/31          10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.1 10.0.0.5
 *        10.10.10.4/31          10.10.10.11           0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.3 10.0.0.5
 *        10.10.10.4/31          10.10.10.4            0       -          100     0       i
 * >      10.10.10.6/31          10.10.10.4            0       -          100     0       i
 * >      10.10.10.8/31          -                     -       -          -       0       i
 *        10.10.10.8/31          10.10.10.3            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.1 10.0.0.5
 *        10.10.10.8/31          10.10.10.7            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.2 10.0.0.5
 *        10.10.10.8/31          10.10.10.8            0       -          100     0       i
 * >      10.10.10.10/31         10.10.10.8            0       -          100     0       i
spine-01#ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 72(100) bytes of data.
80 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=1.21 ms
80 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=0.808 ms
80 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=0.619 ms
80 bytes from 10.0.0.1: icmp_seq=4 ttl=64 time=0.608 ms
80 bytes from 10.0.0.1: icmp_seq=5 ttl=64 time=0.579 ms

--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 6ms
rtt min/avg/max/mdev = 0.579/0.763/1.205/0.234 ms, ipg/ewma 1.442/0.972 ms
spine-01#ping 10.10.10.4
PING 10.10.10.4 (10.10.10.4) 72(100) bytes of data.
80 bytes from 10.10.10.4: icmp_seq=1 ttl=64 time=1.71 ms
80 bytes from 10.10.10.4: icmp_seq=2 ttl=64 time=0.684 ms
80 bytes from 10.10.10.4: icmp_seq=3 ttl=64 time=0.686 ms
80 bytes from 10.10.10.4: icmp_seq=4 ttl=64 time=0.644 ms
80 bytes from 10.10.10.4: icmp_seq=5 ttl=64 time=0.682 ms

--- 10.10.10.4 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 7ms
rtt min/avg/max/mdev = 0.644/0.881/1.713/0.415 ms, ipg/ewma 1.780/1.282 ms
spine-02#sh ip bgp s
BGP summary information for VRF default
Router identifier 10.0.0.5, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Description              Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  leaf-01                  10.10.10.2  4 65000            607       603    0    0 00:25:20 Estab   6      6
  leaf-02                  10.10.10.6  4 65000            604       604    0    0 00:25:14 Estab   6      6
  leaf-03                  10.10.10.10 4 65000            600       599    0    0 00:25:09 Estab   6      6
spine-02#sh ip bgp
BGP routing table information for VRF default
Router identifier 10.0.0.5, local AS number 65000
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >      10.0.0.1/32            10.10.10.2            0       -          100     0       i
 * >      10.0.0.2/32            10.10.10.6            0       -          100     0       i
 * >      10.0.0.3/32            10.10.10.10           0       -          100     0       i
 * >Ec    10.0.0.4/32            10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.4 C-LST: 10.0.0.1
 *  ec    10.0.0.4/32            10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.4 C-LST: 10.0.0.2
 *  ec    10.0.0.4/32            10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.4 C-LST: 10.0.0.3
 * >      10.0.0.5/32            -                     -       -          -       0       ?
 * >      10.10.10.0/31          10.10.10.2            0       -          100     0       i
 * >      10.10.10.2/31          -                     -       -          -       0       ?
 *        10.10.10.2/31          10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.3 10.0.0.4
 *        10.10.10.2/31          10.10.10.2            0       -          100     0       i
 *        10.10.10.2/31          10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.1 C-LST: 10.0.0.2 10.0.0.4
 * >      10.10.10.4/31          10.10.10.6            0       -          100     0       i
 * >      10.10.10.6/31          -                     -       -          -       0       ?
 *        10.10.10.6/31          10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.1 10.0.0.4
 *        10.10.10.6/31          10.10.10.9            0       -          100     0       i Or-ID: 10.0.0.2 C-LST: 10.0.0.3 10.0.0.4
 *        10.10.10.6/31          10.10.10.6            0       -          100     0       i
 * >      10.10.10.8/31          10.10.10.10           0       -          100     0       i
 * >      10.10.10.10/31         -                     -       -          -       0       ?
 *        10.10.10.10/31         10.10.10.1            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.1 10.0.0.4
 *        10.10.10.10/31         10.10.10.5            0       -          100     0       i Or-ID: 10.0.0.3 C-LST: 10.0.0.2 10.0.0.4
 *        10.10.10.10/31         10.10.10.10           0       -          100     0       i
spine-02#ping 10.10.10.4
PING 10.10.10.4 (10.10.10.4) 72(100) bytes of data.
80 bytes from 10.10.10.4: icmp_seq=1 ttl=64 time=1.02 ms
80 bytes from 10.10.10.4: icmp_seq=2 ttl=64 time=0.797 ms
80 bytes from 10.10.10.4: icmp_seq=3 ttl=64 time=0.715 ms
80 bytes from 10.10.10.4: icmp_seq=4 ttl=64 time=0.727 ms
80 bytes from 10.10.10.4: icmp_seq=5 ttl=64 time=0.725 ms

--- 10.10.10.4 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 9ms
rtt min/avg/max/mdev = 0.715/0.796/1.019/0.114 ms, ipg/ewma 2.317/0.902 ms
spine-02#ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 72(100) bytes of data.
80 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=1.69 ms
80 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=0.588 ms
80 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=0.574 ms
80 bytes from 10.0.0.1: icmp_seq=4 ttl=64 time=0.715 ms
80 bytes from 10.0.0.1: icmp_seq=5 ttl=64 time=0.552 ms

--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 7ms
rtt min/avg/max/mdev = 0.552/0.824/1.694/0.438 ms, ipg/ewma 1.770/1.244 ms
