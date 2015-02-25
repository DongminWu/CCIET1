#CCIE T1 版本


##Layer 2

###AS12345

==题目==

1. 根据拓扑图, 保证AS12345里面设备的连通性
2. SW1 和SW2 的E2/0-E2/3 口通过trunk方式互联

==解法==	

**配交换机**
1. vlan, 在每个Ethernet接口
```bash
switchport access vlan XX
switchport mode access
```
2. VTP配置
    
	SW1    :server
```bash
vtp domain CCIE
vtp version 2
vtp mode server
vtp password CCIErock$
```
    SW2		:client
	```bash
    vtp domain CCIE
    vtp version 2
    vtp mode client
  	vtp password CCIErock$
    ```
3. Trunk配置
    ```bash
    int range e2/0-3
    switch port trunk encapsulation dot1q
    switch mode trunk
    ```
4. 改mac aging-time
    ```bash
    mac address-table aging-timg 7200
    ```

### AS34567

==题目==

==解法==

1. 完善Vlan配置(SW4没配)
```bash
switchport access vlan XX
switchport mode access
```

2. VTP
	```bash
vtp domain CCIE
vtp version 2
vtp mode transparent
vtp password CCIErock$
    ```

3. 配SW3-SW4之间的直连链路
```bash
int range e2/0-3
switch port trunk encapsulation dot1q
switch mode trunk
```


###Spanning-Tree

==题目==

==解法==

1. 配置生成树模式, 调整根桥位置
	1. SW1-4
	```bash
    spanning-tree mode rapid-pvst
    ```
    2. 根桥配置
	```bash
spanning-tree vlan 14,24,46 priority          {SW1:4096 SW2:0}
spanning-tree vlan 15,23,57,67 priority       {SW1:0 SW2:4096}
========================================================
spanning-tree vlan 49,89,101,411,999 priority {SW3:0 SW4:4096}
spanning-tree vlan 34,38,310 priority         {SW3:4096 SW4:0}
	```
    3. SW1-4
	```bash
    spanning-tree portfast (SW1,2:edge) default
    ```
    4. SW1-4 BPDU保护
    ```bash
    spanning-tree portfast (SW1,2:edge) bpduguard default
    ```
2. 处理未使用的接口
```bash
int XXX
	switch port mode access
    switchport access vlan 999
	shutdown
```

###PPP

==解法==

1. R18,R19 配置ppp

R18

```bash
int s1/0
encapulation ppp
ppp chap username ACME_R18
ppp chap password ccie
```

R19

```bash
int s1/0
encapulation ppp
ppp chap username ACME_R19
ppp chap password ccie
```

##Layer 3

###OSPF AS12345

1. 配置R1-R7的OSPF协议, 需要配置router-id, 掩码0.255.255.255

R1-R7

```bash
router ospf 12345
router-id 123.X.X.X (X:1-7)
network 123.0.0.0 0.255.255.255 area 0 
```

###EIGRP AS34567

1. 配置R8-R11的EIGRP协议, 掩码0.255.255.255

R8-R11

```bash
router eigrp 34567
net 123.0.0.0 0.255.255.255
```

2. 配置SW3-SW4的EIGRP协议, 掩码0.255.255.255

SW3-SW4

```bash
router eigrp 34567
net 123.0.0.0 0.255.255.255
```


###EIGRP AS45678

1. 配置R15-R17的eigrp要用到别名cisco,记住要进入topology base

R15-R17

```bash
router eigrp cisco
address ipv4 auto 45678
network 123.0.0.0 0.255.255.255
topology base
no auto-summary
```

2. 配置SW5, SW6, R18,R19, 不需要用到别名

SW5, SW6, R18,R19

```bash
router eigrp cisco
network 123.0.0.0 0.255.255.255
topology base
no auto-summary
```

###BGP AS12345

>需要bgp log
>需要update loop 0

1. 将R1配置为AS12345的反射器, 

R1

```bash
router bgp 12345
    no bgp default ipv4
    bgp log
    bgp router-id 123.1.1.1
    neighbor iBGP peer-group
    neighbor iBGP remote-as 12345
    neighbor iBGP update loop 0
    neighbor 123.2.2.2 peer-group iBGP
    neighbor 123.3.3.3 peer-group iBGP
    neighbor 123.6.6.6 peer-group iBGP
    neighbor 123.7.7.7 peer-group iBGP
        address-family ipv4
            neighbor 123.2.2.2 activate
            neighbor 123.3.3.3 activate
            neighbor 123.6.6.6 activate
            neighbor 123.7.7.7 activate
            neighbor iBGP route-reflect-client
```

2. 配置R2, R3, R6, R7

R2, R3, R6, R7

```bash
router bgp 12345
    no bgp default ipv4
    bgp log
    bgp router-id 123.X.X.X (X:2,3,6,7)
    neighbor 123.1.1.1 remote-as 12345
    neighbor 123.1.1.1 update loop 0
    address-family ipv4
         neighbor 123.1.1.1 activate
```

###BGP 34567

>需要update loop 0
>需要redistribute EIGRP
>需要next-hop-self

1. 配置R8-R11, 把其他的配置为邻居

```bash
router bgp 34567
    no bgp default ipv4
    bgp router 123.X.X.X  (X:8,9,10,11)
    neighbor 123.Y.Y.Y remote-as 34567 (Y:8,9,10,11除去X)
    neighbor 123.Y.Y.Y remote-as 34567 (Y:8,9,10,11除去X)
    neighbor 123.Y.Y.Y remote-as 34567 (Y:8,9,10,11除去X)
    neighbor 123.Y.Y.Y update loop 0 (Y:8,9,10,11除去X)
    neighbor 123.Y.Y.Y update loop 0 (Y:8,9,10,11除去X)
    neighbor 123.Y.Y.Y update loop 0 (Y:8,9,10,11除去X)
    address-family ipv4
        neighbor 123.Y.Y.Y activate (Y:8,9,10,11除去X)
        neighbor 123.Y.Y.Y activate (Y:8,9,10,11除去X)
        neighbor 123.Y.Y.Y activate (Y:8,9,10,11除去X)
        neighbor 123.Y.Y.Y next-hop-self (Y:8,9,10,11除去X)
        neighbor 123.Y.Y.Y next-hop-self (Y:8,9,10,11除去X)
        neighbor 123.Y.Y.Y next-hop-self (Y:8,9,10,11除去X)
```
        




