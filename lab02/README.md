### Построение Underlay сети (OSPF)

### Цели
Настроить OSPF для Underlay сети

### Реализация
![схема сети](scheme_2.png)

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
leaf-01#sh run | sec ospf
interface Ethernet7
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet8
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Loopback0
   ip ospf area 0.0.0.0
router ospf 1
   router-id 10.0.0.1
   max-lsa 12000
```

</details>

<details>
<summary><b>leaf-02</b> (нажмите, чтобы раскрыть)</summary>

```cisco
leaf-02# sh run | sec ospf
interface Ethernet7
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet8
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Loopback0
   ip ospf area 0.0.0.0
router ospf 1
   router-id 10.0.0.2
   max-lsa 12000
```

</details>

<details>
<summary><b>leaf-03</b> (нажмите, чтобы раскрыть)</summary>

```cisco
leaf-03# sh run | sec ospf
interface Ethernet7
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet8
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Loopback0
   ip ospf area 0.0.0.0
router ospf 1
   router-id 10.0.0.3
   max-lsa 12000
```

</details>

<details>
<summary><b>spine-01</b> (нажмите, чтобы раскрыть)</summary>

```cisco
spine-01#sh run | sec ospf
interface Ethernet1
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet2
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet3
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Loopback0
   ip ospf area 0.0.0.0
router ospf 1
   router-id 10.0.0.4
   max-lsa 12000
```

</details>

<details>
<summary><b>spine-02</b> (нажмите, чтобы раскрыть)</summary>

```cisco
spine-02#sh run | sec ospf
interface Ethernet1
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet2
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Ethernet3
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
interface Loopback0
   ip ospf area 0.0.0.0
router ospf 1
   router-id 10.0.0.5
   max-lsa 12000
```

</details>

### Проверка соседства/распространения маршрутов
```cisco
spine-01#sh ip ospf nei
Neighbor ID     Instance VRF      Pri State                  Dead Time   Address         Interface
10.0.0.1        1        default  0   FULL                   00:00:31    10.10.10.0      Ethernet1
10.0.0.3        1        default  0   FULL                   00:00:36    10.10.10.8      Ethernet3
10.0.0.2        1        default  0   FULL                   00:00:35    10.10.10.4      Ethernet2
spine-01#
spine-01#sh ip route

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

Gateway of last resort is not set

 O        10.0.0.1/32 [110/20]
           via 10.10.10.0, Ethernet1
 O        10.0.0.2/32 [110/20]
           via 10.10.10.4, Ethernet2
 O        10.0.0.3/32 [110/20]
           via 10.10.10.8, Ethernet3
 C        10.0.0.4/32
           directly connected, Loopback0
 O        10.0.0.5/32 [110/30]
           via 10.10.10.0, Ethernet1
           via 10.10.10.4, Ethernet2
           via 10.10.10.8, Ethernet3
 C        10.10.10.0/31
           directly connected, Ethernet1
 O        10.10.10.2/31 [110/20]
           via 10.10.10.0, Ethernet1
 C        10.10.10.4/31
           directly connected, Ethernet2
 O        10.10.10.6/31 [110/20]
           via 10.10.10.4, Ethernet2
 C        10.10.10.8/31
           directly connected, Ethernet3
 O        10.10.10.10/31 [110/20]
           via 10.10.10.8, Ethernet3

spine-02#sh ip ospf nei
Neighbor ID     Instance VRF      Pri State                  Dead Time   Address         Interface
10.0.0.1        1        default  0   FULL                   00:00:29    10.10.10.2      Ethernet1
10.0.0.3        1        default  0   FULL                   00:00:37    10.10.10.10     Ethernet3
10.0.0.2        1        default  0   FULL                   00:00:37    10.10.10.6      Ethernet2
spine-02#
spine-02#sh ip route

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

Gateway of last resort is not set

 O        10.0.0.1/32 [110/20]
           via 10.10.10.2, Ethernet1
 O        10.0.0.2/32 [110/20]
           via 10.10.10.6, Ethernet2
 O        10.0.0.3/32 [110/20]
           via 10.10.10.10, Ethernet3
 O        10.0.0.4/32 [110/30]
           via 10.10.10.2, Ethernet1
           via 10.10.10.6, Ethernet2
           via 10.10.10.10, Ethernet3
 C        10.0.0.5/32
           directly connected, Loopback0
 O        10.10.10.0/31 [110/20]
           via 10.10.10.2, Ethernet1
 C        10.10.10.2/31
           directly connected, Ethernet1
 O        10.10.10.4/31 [110/20]
           via 10.10.10.6, Ethernet2
 C        10.10.10.6/31
           directly connected, Ethernet2
 O        10.10.10.8/31 [110/20]
           via 10.10.10.10, Ethernet3
 C        10.10.10.10/31
           directly connected, Ethernet3
```
