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
