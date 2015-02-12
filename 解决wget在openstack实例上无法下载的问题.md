## openstack实例中wget下载失败问题
为什么在openstack实例通过wget无法进行下载,例如

    $wget -c http://xxxxx/cloudify-components_3.1.0-ga-b85_amd64.deb
    Connecting to 10.67.190.221:80... connected.
    HTTP request sent, awaiting response... Read error (Connection timed out) in headers.
    Retrying.
    
    $ wget -O - 'https: //ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/autobuild.asc'
    Connecting to ceph.com (ceph.com)|208.113.241.137|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: unspecified [text/plain]
    Saving to: `STDOUT'
    [<=>
  
这个问题可以通过把MTU从默认的1500改为1400可以解决  

    $ sudo ip link set mtu 1400 dev eth0
    $ sudo ip link show dev eth0
    2: eth0:  mtu 1400 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:85:ee:a5 brd ff:ff:ff:ff:ff:ff

这意味着OpenStack DHCP的MTU应该被修正为1400

如果neutron使用DHCP agent,但是dhcp-option-force=26,1400 没有在OpenVSwitch and GRE上设置,
像在主机网卡上通过tcpdump上显示的那样,每个主机需要1500的MTU需要被分段.

    IP 10.111.1.2 > 10.111.1.1: ICMP 10.111.1.2 unreachable -
    need to frag (mtu 1445), length 556
   
当OpenVSwitch 在GRE上使用TINC接口, 设置TINC遵守的DF标志, 网络包就不会被分段,到达不了主机. 当GRE使用当OpenVSwitch的时候,puppet neutron模块可能自动添加该选项

## 参考
[wget on an OpenStack instance hangs ? Try lowering the MTU](http://dachary.org/?p=2570)
