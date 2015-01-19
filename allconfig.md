###SW1

int ran e0/0-3
sw mo ac
int ran e1/0-3
sw mo ac
int e0/0
sw ac vl XX
...
int ran e2/0-3
sw tr en dot
sw mo tr
mac addr aging 7200
vtp domain CCIE
vtp ver 2
vtp mo server
vtp pass CCIErock$
span mo rapid
span vl XX,XX,XX,999 pri 0
span vl XX,XX pri 4096
span portfast edge def
span portfast edge bpdug def
int ran 3/0-3
sw mo ac
sw ac vl 999
shutdown

###SW2

int ran e0/0-3
sw mo ac
int ran e0/1-3
sw mo ac
int e0/0
sw ac vl XX
....
int e2/0-3
sw tr en dot
sw mo tr
vtp domain CCIE
vtp ver 2
vtp mo client
vtp pass CCIErock$
span mo rapid
span vl XX,XX,XX,999 pri 4096
span vl XX,XX,XX pri 0
span port ed def
span port ed bpdug def
int e3/0-3
sw ac vl 999
sw mo ac
shut


###SW3

int ran e0/0-3
sw mo ac
int ran e1/0-3
sw mo ac
int e0/0
sw ac vl XX
int ran e2/0-3
sw tr en dot
sw mo tr
vtp domain CCIE
vtp ver 2
vtp mo transp
vtp pass CCIErock$
int ran e1/0-3
sw ac vl 999
sw mo ac
shur
int ran e3/0-3
sw mo ac
sw ac vl 999
shut
router eigrp 34567
network 123.0.0.0 0.255.255.255
**int vl 34**
delay 100

###SW4
int ran e0/0-3
sw mo ac
int ran e1/0-3
sw mo ac
int e0/0
sw ac vl XX
int ran e2/0-3
sw tr en dot
sw mo tr
vtp domain CCIE
vtp ver 2
vtp mo tran
vtp pass CCIErock$
int ran 3/0-3
sw ac vl 999
sw mo ac
shut
router eigrp 34567
network 123.0.0.0 0.255.255.255
**int vl 34**
delay 100


###R18
int s1/0
en ppp
ppp chap user ACME_R18
ppp chap pass ccie


###R19
int s1/0
en ppp
ppp chap user ACME_R19
ppp chap pass ccie

###R1
router ospf 12345
router-id 123.1.1.1
network 123.0.0.0 0.255.255.255 area 0
router bgp 12345
bgp log
no bgp def ipv4
bgp router-id 123.1.1.1
nei iBGP peer
nei iBGP remote 12345
nei iBGP upda lo 0
nei 123.2.2.2 peer iBGP
nei 123.3.3.3 peer iBGP
nei 123.6.6.6 peer iBGP
nei 123.7.7.7 peer iBGP
addr ipv4
nei 123.2.2.2 act
nei 123.3.3.3 act
nei 123.6.6.6 act
nei 123.7.7.7 act
nei iBGP route-reflect-client:w


###R2
router ospf 12345
router-id 123.2.2.2
network 123.0.0.0 0.255.255.255 area 0
router bgp 12345
bgp log
no bgp def ipv4
bgp router-d 123.2.2.2
nei 123.1.1.1 remo 12345
nei 123.1.1.1 update lo 0
addr ipv4
nei 123.1.1.1 act
ip vrf GREEN
rd 12:12
route 12:12
ip vrf BLUE
rd 13:13
route 13:13
ip vrf RED
rd 14:14
route 14:14
ip vrf YELLOW
rd 15:15
route 15:15
ip vrf INET
rd 99:99
route 99:99
int e1/0
no shutdown
int e1/0.12
en dot 12
ip vrf for GREEN
ip address 10.201.12.1 255.255.255.252
int e1/0.13
en dot 13
ip vrf fo BLUE
ip address 10.201.13.1 255.255.255.252
int e1/0.14
en dot 14
ip vrf fo RED
ip address 10.201.14.1 255.255.255.252
int e1/0.15
en dot 15
ip vrf for YELLOW
ip address 10.201.15.1 255.255.255.252
int e1/0.99
en dot 99
ip vrf for INET
ip address 10.201.99.1 255.255.255.252
router bgp 12345
addr ipv4 vrf GREEN
nei 10.201.12.2 remote 65112
nei 10.201.12.2 active 
exit
addr ipv4 vrf BLUE
nei 10.201.13.2 remote 65112
nei 10.201.13.2 act
exit
addr ipv4 vrf RED
nei 10.201.14.2 remote 65112
nei 10.201.14.2 act
exit
addr ipv4 vrf YELLOW
nei 10.201.15.2 remote 65112
nei 10.201.15.2 act
addr ipv4 vrf INET
nei 10.201.99.2 remote 65112
nei 10.201.15.2 act



###R3
router ospf 12345
router-id 123.3.3.3
network 123.0.0.0 0.255.255.255 area 0
router bgp 12345
bgp log
no bgp def ipv4
bgp router-id 123.3.3.3
nei 123.1.1.1 remote 12345
nei 123.1.1.1 update lo 0
addr ipv4
nei 123.1.1.1 act

###R4
router ospf 12345
router-id 123.4.4.4
network 123.0.0.0 0.255.255.255 area 0

###R5
router ospf 12345
router-id 123.5.5.5
network 123.0.0.0 0.255.255.255 area 0


###R6
router ospf 12345
router-id 123.6.6.6
network 123.0.0.0 0.255.255.255 area 0
router bgp 12345
bgp log
no bgp def ipv4
bgp router 123.6.6.6
nei 123.1.1.1 remote 12345
nei 123.1.1.1 update lo 0
addr ipv4
nei 123.1.1.1 act



###R7
router ospf 12345
router-id 123.7.7.7
network 123.0.0.0 0.255.255.255 area 0
router bgp 12345
bgp log
no bgp def ipv4
bgp router 123.7.7.7
nei 123.1.1.1 remote 12345
nei 123.1.1.1 update lo 0
addr ipv4
nei 123.1.1.1 act


###R8
router eigrp 34567
network 123.0.0.0 0.255.255.255
router bgp 34567
no bgp def ipv4
bgp router 123.8.8.8
nei 123.9.9.9 remote 34567
nei 123.10.10.10 remote 34567
nei 123.11.11.11 remote 34567
**nei 123.9.9.9 updat lo 0 **
**nei 123.10.10.10 update lo 0**
**nei 123.11.11.11 update lo 0**

addr ipv4
nei 123.9.9.9 act
nei 123.10.10.10 act
nei 123.11.11.11 act
redis eigrp 34567

###R9
router eigrp 34567
network 123.0.0.0 0.255.255.255
router bgp 34567
no bgp def ipv4
bgp router 123.9.9.9
nei 123.8.8.8 remote 34567
nei 123.10.10.10 remote 34567
nei 123.11.11.11 remote 34567
**nei 123.8.8.8 up lo 0 **
**nei 123.10.10.10 up lo 0 **
**nei 123.11.11.11 up lo 0 **

addr ipv4
nei 123.8.8.8 act
nei 123.10.10.10 act
nei 123.11.11.11 act
redis eigrp 34567
router bgp 34567
nei 30.34.1.1 remote 30000
bgp local 500
addr ipv4
nei 30.34.1.1 act

router eigrp 34567
redis bgp 34567 metirc 10000 100 255 1 1500 route-map b2e
perfix-list default per 0.0.0.0/0
route-map b2e permit 10
match ip address prefix-list default


###R10
router eigrp 34567
network 123.0.0.0 0.255.255.255
router bgp 34567
no bgp ipv4
bgp route 123.10.10.10
nei 123.8.8.8 remote 34567
nei 123.9.9.9 remote 34567
nei 123.11.11.11 remote 34567
**nei 123.8.8.8 up lo 0 **
**nei 123.9.9.9 up lo 0 **
**nei 123.11.11.11 up lo 0 **

addr ipv4
nei 123.8.8.8 act
nei 123.9.9.9 act
nei 123.11.11.11 act
redis eigrp 34567


###R11
router eigrp 34567
network 123.0.0.0 0.255.255.255
router bgp 34567
no bgp ipv4
bgp router 123.11.11.11
nei 123.8.8.8 remote 34567
nei 123.9.9.9 remote 34567
nei 123.10.10.10 remote 34567
**nei 123.8.8.8 up lo 0 **
**nei 123.9.9.9 up lo 0 **
**nei 123.10.10.10 up lo 0 **
addr ipv4
nei 123.8.8.8 act
nei 123.9.9.9 act
nei 123.10.10.10 act
redis eigrp 34567
router bgp 34567
nei 30.34.2.1 remote 30000
bgp local-pre 400
addr ipv4
nei 30.34.2.1 act

router eigrp 34567
redis bgp 34567 metric 10000 100 255 1 1500 route-map b2e
ip prefix default permit 0.0.0.0/0
route-map b2e permit 10
match ip addr pre default


###R15
router eigrp cisco 
addr ipv4 auto 45678
network 123.0.0.0 0.255.255.255
topo base
no auto
router bgp 45678
no bgp def ipv4
bgp router-id 123.15.15.15
nei 103.45.1.1 remote 10003
addr ipv4
nei 103.45.1.1 act
redistri eigrp *45678*

router eigrp cisco
addr ipv4 auto 45678
topo base
redis bgp 45678 metric 1000 100 1 255 1500

###R16
router eigrp cisco
addr ipv4 auto 45678
network 123.0.0.0 0.255.255.255
topo base
no auto 
router bgp 45678
no bgp ipv4
bgp router 123.16.16.16
nei 203.45.16.1 remote 20003
addr ipv4
nei 203.45.16.1 act

###R17
router eigrp cisco 
addr ipv4 auto 45678
**network 123.0.0.0 0.255.255.255**
topo base
no auto


###R18
router eigrp 45678
**network 123.0.0.0 0.255.255.255**
no auto

###R19
router eigrp 45678
**network 123.0.0.0 0.255.255.255**
no auto

###SW5
router eigrp 45678
network 123.0.0.0 0.255.255.255
no auto

###SW6
router eigrp 45678
network 123.0.0.0 0.255.255.255
no auto

###R20
int e1/0
no shutdown
int e1/0.12
en dot 12
ip addr 10.201.12.2 255.255.255.252
int e1/0.13
en dot 13
ip addr 10.201.13.2 255.255.255.252
int e1/0.14
en dot 14
ip addr 10.201.14.2 255.255.255.252
int e1/0.15
en dot 15
ip addr 10.201.15.2 255.255.255.252
int e1/0.99
en dot 99
ip addr 10.201.99.2 255.255.255.252
int e1/1
no shutdown
router bgp 65112
no bgp def ipv4
bgp router-id 123.20.20.20
nei 10.201.12.1 remote 12345
nei 10.201.12.5 remote 12345
nei 10.201.13.1 remote 12345
nei 10.201.13.5 remote 12345
nei 10.201.14.1 remote 12345
nei 10.201.14.5 remote 12345
nei 10.201.15.1 remote 12345
nei 10.201.15.5 remote 12345
nei 10.201.99.1 remote 12345
nei 10.201.99.5 remote 12345
addr ipv4
nei 10.201.12.1 act
nei 10.201.12.1 def
nei 10.201.12.5 act
nei 10.201.12.5 def
nei 10.201.13.1 act
nei 10.201.13.1 def
nei 10.201.13.5 act
nei 10.201.14.5 def
nei 10.201.15.1 act
nei 10.201.15.1 def
nei 10.201.15.5 act
nei 10.201.15.5 def
nei 10.201.99.1 act
nei 10.201.99.5 act
network 10.201.1.1 255.255.255.255
network 10.201.2.1 255.255.255.255
network 123.20.20.20 255.255.255.255
aggre 10.0.0.0 255.0.0.0 summ
aggre 123.0.0.0 255.0.0.0 summ

