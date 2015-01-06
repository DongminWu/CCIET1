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
    mac addredss-table aging-timg 7200
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