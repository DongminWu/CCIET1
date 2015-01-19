#Layer2

##AS12345

*SW1,2*

1. vlan
2. vtp
3. trunk
4. SW1: mac aging-time

##AS34567

*SW3,4*

same with *AS12345*
change the vtp mode to *transparent*

##Spanning-Tree

*SW1-4:*

1. mode config to rapid-pvst
2. priority of every vlan in each SW
	SW1,3 odd:0 even:4096
	SW2,4 odd:4096 even:0
3. portfast
4. postfast bpduguard
5. shut the free port of SW1-SW4
	vlan 999 & shutdown

##PPP

*R18 R19*

1. set up serial port 1/0, encapsulation ppp
2. enable ppp chap in serial: hostname:ACME_R1X, password ccie


#Layer3

##AS12345 OSPF

*R1-R7*

1. set up ospf router-id
2. set up network (be attention to "area 0")

##AS 34567 EIGRP

1. *R8-R11, SW3 SW4* set up network in eigrp
2. set `delay 100` of vlan 34 in SW3 SW4

##AS 45678 EIGRP

*R15-17*

1. set eigrp cisco through autonomous system 45678
2. set network
3. topology and no auto-summary

*R18 R19 SW5 SW6*

1. set eigrp 45678 network
2. no auto-summary

##AS12345 BGP

*R1*

1. set up bgp, open log, no unicast
2. peer-group iBGP 
2. set iBGP as 12345
3. peer-group other routers (R2,R3, R6 R7)
4. goto address-family
5. iBGP route-reflector-client
6. activate R2, R3, R6,R7

*R2 R3 R6 R7*

1. set up bgp open log, no unicast
2. set R1 as 12345
3. activate R1



---------------------------------------------



#Layer3

##AS 65112 BGP

*R2 R3*

1. configuarate ip vrf GREEN/BLUE/..../INET rd/route-target XX:XX(12 to 15, 99)
2. open e1/0
3. set up e1/0.12-0.99 
4. -(encapsulate )
5. -(vrf forwarding COLOR)
6. -set up ip address
7. into bgp **12345**
8. goto addr-family
8. set up vrf as 65112
9. active vrf

*R20*

1. open e1/0
2. set up e1/0.12-0.99
3. encapsulate
4. set ip address
5. set up e1/1 as e1/0
6. into bgp **65112**
7. no bgp ipv4
8. sei up route id
9. set up 10.201.XX.1 and 10.201.XX.5 as 12345
10. goto address-family
11. active 10.201.XX.1 and 10.201.XX.5
12. default-originate 10.201.XX.1 and 10.201.XX.5 **(no 99)**
13. network 10.20.1/2.1,123.20.20.20 mask FF.FF.FF.FF
14. aggregate 10.0.0.0 and 123.0.0.0


##AS 34567 BGP

*R8 R9 R10 R11*

1. set up bgp no ipv4, router-id
2. set other router as 34567, update lo
3. goto address-family
4. active and next-hop-self other router
5. **redistribute**


*R9*
1. neighbor 30.34.1.1 as 30000
2. bgp local-preference 500
3. go to addr-family and active 30.34.1.1
4. eigrp redistribute metric 1000 100 255 1 1500 b2e
5. prefix-list permit
6. goto b2e permit
7. match ip addr perfix


*R11*
1. bgp 34567
2. nei 30.34.2.1 as 3W
3. bgp preference 400
4. nei 30.34.2.1 act
5. eigrp redistribute metric 1000 100 255 1 1500 b2e
6. prefix-list permit
7. goto b2e permit
8. match ip addr perfix

##AS 45678 BGP

*R15*

1. set up bgp no ipv4, router-id
2. nei 103.45.1.1 as 10003
3. addr ipv4: 103.145.1.1 active
4. redistribute eigrp
5. aggregate 123.20.1.0
6. eigrp cisco->45678->topology-> redistribute bgp metric *no b2e*

*R16 R17 R18 R19*

1. bgp 45678(R18/R19:65222)
2. no bgp ipv4, router id
3. nei 203.45.16.1 as 20003
4. addr family ipv4, nei 203.45.16.1 active
5. set network 0.0.0.0 as backdoor

*R18 R19*

1. eigrp stub




-----------------------------------------------



```
*Note*
for ipv6 setting
you need to run ipv6 unicast-routing first
```

##OSPF v3

*SW3 SW4*

1. unicast
2. router-id
3. Lo 0 and vlan 34: ospf area 0
4. vlan34 ospf priority

## ipv6 BGP

*R10 R11*

1. unicast-routing
2. into bgp
2. remote as 2001:34:1::1 as 20001
3. af ipv6
4. 2001:34:1::1 act
5. redistribute internal external
6. ospf redistribute bgp 

*R12 R14*

1. router 65111 remote as 20001
2. af ipv6, activate
3. network 2001:123::12:12:12/128, 2001:CC1E:1234:12::/64

## BGP policy

*R2 R3 R6 R7*

1. prefix 123 permit 123.0.0.0/8 le 32
2. into bgp af, 101.123.1.1 prefix 123 out

*R8 R9 R10 R11*

1. prefix 123 permit 123.0.0.0/8 le 32
2. into bgp af, 101.34.1.1 prefix 123 out

*R13*

neighbor 202.65.1.1 weight 1000

##VPN

###VPNv4 neighbor

*R2 R3 R6 R7*

1. bgp af vpnv4
2. activate neighbor 123.1.1.1

*R1*

1. bgp af vpnv4
2. activate 2,3,6,7 123.X.X.X

###LDP neighbor

1. AS12345, all used router interface
2. mpls ldp router-id loopback 0 force
3. int x-> mpls ip

*R2 R3 R6 R7*
 no mpls ip propagate-ttl


###adjust R20 permit

*R20*

1. prefix a permit 1.2.3.4/32
2. route-map abc permit 10
3. match prefix a
3. set weight 100
4. route-map abc permit 20
5. bgp af neighbor 10.201.99.5 router-map abc in

## DMVPN

```
same Part:
	no ip redirect
	tunnel mode gre multi-point
	tunnel source sX/0 (17:2/18,19:1)
	tunnel key 45678
	ip address (17: 10.18.19.1/18,19: 10.18.19/19.19) 255.255.255.0
	ip nhrp network-id 45678
	ip nhrp authentication 45678key
--------------------
same Part 2:
	ip nhrp hold time 300
	bandwith 1000
	delay 1000
	ip mtu 1400
	ip tcp adjust-mss 1360
```

*R17*

1. same Part
2. ip nhrp redirect
3. ip nhrp multicast dynamic
3. same Part2
4. no ip spilt-horizon eigrp 45678

*R18*

1. same Part
2. ip nhrp multicast 203.45.17.2/1 
3. ip nhrp 10.18.19.1 203.45.17.2/1 
4. ip nhrp nhs 10.18.19.1
5. ip nhrp shortcut
6. same Part2

###Encryption

*R17 R18 R19*

1. crypto isakmp policy 10
2. encryption aes
3. authentication pre-share
4. group 2
5. crypto isakmp key CCIE address 0.0.0.0
6. crypto ipsec transform-set CCIEXFORM esp-aes
7. mode transport
8. crypto ipsec profile DMVPNPROFILE
9. set transform-set CCIEXFORM
10. goto int tunnel 0
11. tunnel protection ipsec profile DMVPNPROFILE




----------------------------------------------



##Multicast

*R15 R16 R17 R18 R19 SW5 SW6*

1. ip multicast-routing
2. goto int x
3. ip pim sparse-mode

*R15*

1. int Loopback 0 -> ip pim sparse-mode
2. ip pim rp-candidate lo 0
3. ip pim bsr-candidate lo 0

*R18 R19*
1. int tu 0->ip pim prase-mode
2. int e0/0->ip pim prase-mode 
3. ip igmp join-group 232.1.1.1

##Security

*R20*

banner login #Caution! No unauthorized access!#

*SW3*

1. int e0/0-3
2. switchport port-security->mac-address sticky-> maximum 1-> viodation shutdown

##Advance network services

*R20 SSh*

1. ip domian-name cisco.com
2. username test privilege 1 password test
3. crypto key generate rsa
4. 768
5. ip ssh maxstartups 5
6. ip ssh logging events
7. into line vty 0 4
8. login local
9. transport input ssh
10. access-class 1 in
11. access-list 1 permit 123.10.2.0 0.0.0.255

*R20 NAT*

1. access-list 2 permit 10.1.0.0 0.255.255
2. ip nat inside source list 2 int loop 0 overload
3. int eX/X.99
4. ip nat outside
5. ip eX/X.XX
6. ip nat inside

*R17*

1. ip cef
2. int tu 0
3. ip flow egress
4. ip flow-top-talker
5. sort-by bytes
6. cache-timeout 10000
7. top 10
8. show ip flow top-talkers

###NTP

*SW3*

1. ntp master
2. ntp source loop 0

*R10 R12*

1. ntp server 2001:123::3:3:3
2. ntp source loop 0
