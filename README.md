# OpenWrt_VBox
Source from 
https://www.youtube.com/watch?v=Z13R6m4nYzI

This is a short video explaining how you can set up OpenWrt[1] Barrier Breaker 14.07 as an x86 virtual machine (VM) using VirtualBox. OpenWrt is mainly used to install on SOHO router to replace their stock firmware with a better maintained and open-source alternative. But, you can also install it as a VM to play around with to do some testing before flashing and configuring your router.

So what is OpenWrt?
===============
-- Omitted due to exceeding the maximum length of description --

What you'll need
============
1: A host system (Ubuntu is demonstrated here).
2: VirtualBox installed on the host system.
3: And obviously an internet connection.

More information
============
1: Official OpenWrt website
https://openwrt.org/
2: Official OpenWrt download page
http://downloads.openwrt.org/
3: Official VirtualBox website
https://www.virtualbox.org/
4: Official Independent Security Evaluators (ISE) website
http://securityevaluators.com/
5: Official Electronic Frontier Foundation (EFF) website
https://www.eff.org/
6: Official ISE / EFF SOHOpelesslybroken website
http://www.sohopelesslybroken.com/

Notes
====
1. Go to downloads.openwrt.org and grab the latest OpenWrt x86 image (14.07 as this time of this vid):
wget http://downloads.openwrt.org/barrier_...

2. Extract the gzipped raw OpenWrt image:
gunzip openwrt-x86-generic-combined-ext4.img.gz

3. Convert the raw image to a VDI image:
VBoxManage convertfromraw --format VDI openwrt-x86-generic-combined-ext4.img openwrt-x86-generic-combined-ext4.vdi

4. Add a new VM with this image as its disk and configure NICs.

5. Boot the VM and configure a password for root:
passwd

6. By default, OpenWrt assigns its first NIC a static IP address of 192.168.1.1/24. This may not be nice if this doesn't reflect your current subnet, or if that IP address is already used by another device (such as your own router). The following commands configure the first NIC to request an IP address from a DHCP server that's on the subnet the NIC is connected to. This means that you MUST have a DHCP server running on that subnet, or you should configure a static IP address:
uci delete network.lan.ipaddr
uci delete network.lan.netmask
uci delete network.lan.ip6assign
uci set network.lan.proto=dhcp
uci commit
ifdown lan
ifup lan

7. SSH into your box to utilize your copy-paste skills.

8. Also, since I have configured a second NIC, we can configure that NIC to be our WAN-facing NIC. Of course, since both my NICS are bridged in VirtualBox, mine happens to be in the same subnet as my LAN-facing NIC, but this could well be in another subnet, i.e. the subnet which your ISP has provided you with:
uci set network.wan=interface
uci set network.wan.ifname=eth1
uci set network.wan.proto=dhcp
uci commit
ifup wan

9. Update your packages. Sadly there's no 'opkg upgrade' so the 2nd command looks weird but provides the same thing. If the second command doesn't do anything, your packages are probably already updated to the latest versions:
opkg update
eval $(opkg list_upgradable | sed 's/ - .*//' | sed 's/^/opkg upgrade /')

10. To install a nice WebUI:
opkg install luci-ssl

11. To only allow HTTPS:
vi /etc/config/uhttpd
# list listen_http '0.0.0.0:80'
# list listen_http '[::]:80'
/etc/init.d/uhttpd reload


-------------
MultiWan support :
--------------
http://wiki.openwrt.org/doc/uci/multiwan
http://wiki.openwrt.org/doc/howto/mwan3

------------------
IPSec VPN support :
------------------
http://wiki.openwrt.org/doc/howto/packet.scheduler/packet.scheduler

--------------
Traffic Control support :
-----------------------
http://wiki.openwrt.org/doc/howto/packet.scheduler/packet.scheduler

---------------
ADSL Support :
-------------
http://wiki.openwrt.org/inbox/adsl_support

