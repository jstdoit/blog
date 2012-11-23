---
layout: post
title: "configure l2tp VPN on ubuntu VPS"
date: 2012-11-22 02:47
comments: true
categories: [vpn]
---
the [Google](http://google.com) of my country is sometimes blocked, but my work need that very much. As a coder, how can I tolerate such? as a result, i started my VPN server.
below is my way how to install l2tp/ipsec service on my vps(os backed by Ubuntu 10.04).
<!-- more -->
* firstly you need the IPSec component for security or auth purpose.
{% codeblock lang:bash %}
sudo aptitude install openswan
{% endcodeblock %}
* after install the IPSect, you need to make some change to `/etc/ipsec.conf`, you can simply change that file with content as follows:
{% codeblock /etc/ipsec.conf lang:bash %}
version 2.0
config setup
    nat_traversal=yes
    virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12
    oe=off
    protostack=netkey

conn L2TP-PSK-NAT
    rightsubnet=vhost:%priv
    also=L2TP-PSK-noNAT

conn L2TP-PSK-noNAT
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    rekey=no
    ikelifetime=8h
    keylife=1h
    type=transport
    left=YOUR.SERVER.IP.ADDRESS
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
{% endcodeblock %}
then change the file `/etc/ipsec.secrets` with content as follows:
{% codeblock /etc/ipsec.secrets lang:bash%}
YOUR.SERVER.IP.ADDRESS   %any:  PSK "YourSharedSecret"
{% endcodeblock %}
**Important: You should remembter to change `YOUR.SERVER.IP.ADDRESS` and `YourSharedSecret` accordingly.**
* Run the following command for `openswan` to stop complaining
{% codeblock lang:bash %}
for each in /proc/sys/net/ipv4/conf/*
do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
done
{% endcodeblock lang:bash %}
* Check if IPSec is correctly setup
{% codeblock lang:bash %}
sudo ipsec verify
{% endcodeblock %}
make sure all items are correct.

* now restart `openswan`
{% codeblock lang:bash %}
sudo /etc/init.d/ipsec restart
{% endcodeblock %}
* Now you can add a L2TP/IPSec connection on your OS X and see if IPSec is working. Use whatever account and password. We are not there yet. The only thing you need to make sure is that you connect to the right server with the right shared secret as specified in `/etc/ipsec.secrets` on your server.
Monitor /var/log/system.log on your OS X by running
{% codeblock lang:bash %}
tail -f /var/log/system.log
{% endcodeblock %}
while OS X is trying to connect to your server via L2TP/IPSec. It will fail eventually because we haven’t configured L2TP yet, but if you see a line in the system log saying something like
{% codeblock lang:bash %}
Apr 30 18:12:48 bender pppd[1507]: IPSec connection established
{% endcodeblock %}
Now, everything goes!
* Next big step is to install L2TP, execute command below to install `xl2tp` for l2tp use:
{% codeblock lang:bash %}
sudo aptitude install xl2tpd
{% endcodeblock %}
* change the `/etc/xl2tpd/xl2tpd.conf` file with contents of :
{% codeblock lang:bash %}
[global]
ipsec saref = yes

[lns default]
ip range = 10.1.2.2-10.1.2.255
local ip = 10.1.2.1
refuse chap = yes
refuse pap = yes
require authentication = yes
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
{% endcodeblock %}
* now create the file `/etc/ppp/options.xl2tpd` if not exists, contents as follows:
{% codeblock lang:bash %}
require-mschap-v2
ms-dns 8.8.8.8
ms-dns 8.8.4.4
asyncmap 0
auth
crtscts
lock
hide-password
modem
debug
name l2tpd
proxyarp
lcp-echo-interval 30
lcp-echo-failure 4
{% endcodeblock %}
here we use Google Public DNS in the ms-dns field, if you prefer another one, just feel free to change that.
* now you can add a vpn user to file `/etc/ppp/chap-secrets` and try to see if l2tp works.
{% codeblock lang:bash %}
# user      server      password            ip
test        l2tpd       testpassword        *
{% endcodeblock %}
* restart `xl2tpd` via
{% codeblock %}
sudo /etc/init.d/xl2tpd restart
{% endcodeblock %}
* if you use iptables for firewalling, make sure it forwards packets so you can browse the Interent after connecting to VPN. Run the following command
{% codeblock lang:bash %}
iptables --table nat --append POSTROUTING --jump MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
{% endcodeblock %}
* sometimes the vpn turn out to be stoped by some reasones, so create a file `vpn.sh` with content as followings and make it executable via `chmod u+x vpn.sh`
{% codeblock lang:bash %}
#!/usr/bin/env bash
/etc/init.d/ipsec restart
/etc/init.d/xl2tpd restart
iptables --table nat --append POSTROUTING --jump MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
{% endcodeblock %}
* * *
* here i capture some screenshot when i configure L2TP on my MAC.
![configure l2tp on MAC](/images/mac01.png "configure l2tp server info.")
![configure l2tp on MAC](/images/mac02.png "configure l2tp auth info.")
Reference 
[Configure L2TP/IPSec VPN on Ubuntu](http://blog.riobard.com/2010/04/30/l2tp-over-ipsec-ubuntu)
