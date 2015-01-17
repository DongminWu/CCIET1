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

1. router 65112 remote as 20001
2. af ipv6, activate
3. network 2001:123:12:12:12/128, 2001:CC1E:1234:12::/64

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

