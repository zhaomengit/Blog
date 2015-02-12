Why would OpenStack instances fail to wget a URL and work perfectly on others ? For instance:

> $ wget -O - 'https: //ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/autobuild.asc'
  Connecting to ceph.com (ceph.com)|208.113.241.137|:443... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: unspecified [text/plain]
  Saving to: `STDOUT'
  [<=>
  
If it can be fixed by lowering the MTU from the default of 1500 to 1400 with:

> $ sudo ip link set mtu 1400 dev eth0
  $ sudo ip link show dev eth0
    2: eth0:  mtu 1400 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:85:ee:a5 brd ff:ff:ff:ff:ff:ff

it means the underlying OpenStack DHCP should be fixed to set the MTU to 1400.

If neutron is used as a DHCP agent but the dhcp-option-force=26,1400 was not set as instructed on top of OpenVSwitch and GRE, all instances will get an MTU of 1500 which would require fragmentation, as displayed by tcpdump on the host interface:

> IP 10.111.1.2 > 10.111.1.1: ICMP 10.111.1.2 unreachable -
   need to frag (mtu 1445), length 556
   
When OpenVSwitch uses tinc interfaces for GRE, it sets the DF flag which is obeyed by tinc and the packet is not fragmented and never reaches destination. The puppet neutron module should probably support adding this option automatically when OpenVSwitch is used with GRE.

**One Response to wget on an OpenStack instance hangs ? Try lowering the MTU**
alexandre derumier says:
February 7, 2014 at 8:11 am
Hi Loic,

I think that using mss clamping could also work. (I use that for ipsec tunnel or ppoe connexions)

It’s possible to do it with iptables in mangle table (but I’m not sure it’s work with openvswitch gre)

iptables -t mangle -A POSTROUTING -p tcp –tcp-flags SYN,RST SYN -o eth0 -j TCPMSS –set-mss 1460

http://www.linuxtopia.org/Linux_Firewall_iptables/x4700.html
