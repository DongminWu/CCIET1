#Layer2
##AS12345
==SW1,2==
1. vlan
2. vtp
3. trunk
4. SW1: mac aging-time

##AS34567
==SW3,4==
same with *AS12345*
change the vtp mode to *transparent*

##Spanning-Tree
==SW1-4:==
1. mode config to rapid-pvst
2. priority of every vlan in each SW
	SW1,3 odd:0 even:4096
	SW2,4 odd:4096 even:0
3. portfast
4. postfast bpduguard

5. shut the free port of SW1-SW4
	vlan 999 & shutdown

##PPP
==R18 R19==
1. set up serial port 1/0, encapsulation ppp
2. enable ppp chap in serial: hostname:ACME_R1X, password ccie


#Layer3
##AS12345 OSPF
==R1-R7==
1. set up ospf router-id
2. set up network (be attention to "area 0")

##AS 34567 EIGRP
1. ==R8-R11, SW3 SW4== set up network in eigrp
2. set `delay 100` of vlan 34 in SW3 SW4

##AS 45678 EIGRP
==R15-17==
1. set eigrp cisco through autonomous system 45678
2. set network
3. topology and no auto-summary

==R18 R19 SW5 SW6==
1. set eigrp 45678 network
2. no auto-summary

##
