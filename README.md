# Hacking Inteno XG6846

* This README was posted on new [blog](https://nr1.nu/blog/2023/01/11/hacking-inteno-xg6846/) ~1-2month ago, sharing it on github for fun: 

__Inteno XG6846 is installed in millions of Swedes' homes and it sits between the router and the switch! With some operators, it is a requirement to be able to watch TV. Now it's time for take control over this device!__

In 2022 there has been a new law in Sweden where the government are allowed to hack people's devices according to the law and regardless of 
whether you are a criminal or not, this is of course NOT something I think is good, so that's why I took hold of the matter and thought that 
I do this as a fun challenge. _Here is the result_

<!-- more -->

## Login Credentials

Internet Providers password was good, but not good enough 

```
Telia:

Username......: suport
Password......: x3faqmiup3fe
```

![Screenshot](https://i.imgur.com/m1YiC97.png)

The XG have dual image banks for firmware file in the FLASH memory.
The firmware file is loaded into each bank and if one bank fails the other one will be used.

## Firmware Naming

![Screenshot](https://i.imgur.com/sayUl5u.png)

## FLASH Memory /Dual bank

![Screenshot](https://i.imgur.com/jYc21c5.png)
![Screenshot](https://i.imgur.com/GJUJrrX.png)


 * This board is from 2012 but they write it's a 2018 product. Another board from Inteno was from 2011 but they claimed it was from 2022. Lol

![Screenshot](https://i.imgur.com/XE0m1ty.png)
![Screenshot](https://i.imgur.com/xRJSduO.png)
![Screenshot](https://i.imgur.com/PcmSzXr.jpg)

## Factory Default / Restore Default

Press Reset button and hold for more than `10 sec` then release or press button `10 times` 
as fast as you want for do a complete reset, everything below `10 seconds`/`10 click` within a limit of time
will do a soft reset.

## Examples

Inteno always using their mac address in their firmware or at least as long as 
I can remember and this unit is no exception on that front, here is an example how device will try launch a firmware upgrade:

HW Adress: `11:22:33:44:55:66` will try to download file `11223445566.conf` if download file is specified as `$MAC$.conf`

This means that each `CPE` can have its own `$mac$.conf` file in TFTP/HTTP server.

If a `one-file-to-many` type of setup is need the operator can specify one file that should be loaded to all `XG6846` in the `DHCP` options.

The configuration file is a XML file containing parameters and values.

!!! Note "Note" 
    The `Inteno XG684x` have limited `security mechanism` for detecting error on the XML configuration so take **GREAT** care when editing and creating these files.

    Best way to create a valid file is to build the configuration in the WEBGUI and then after verification extract the file and then do possible modification if you have access to webui and if not, you know how to get access and  it's not ugly to `borrow` someone else's, if you ask me, but I *don't* recommend that to anyone and I don't do this myself of course! ;-)

## Firmware Upgrade

`Inteno XG684` can be upgraded through the `XML configuration`.

## 3DES Encryption

The XML configuration file can be encrypted using `3DES` if higher security is required.

All `XG6846 PCB boards` have an `preconfigured` unique 3DES keys installed at production. 3DES keys can be retrieved from your Inteno logistics or from your Inteno sales contact. The encryption is 3DES using no salt and no IV. The 3DES key must be converted to hex format.

This script can be done to create configuration for firmware upgrade

```bash
#!/bin/bash
pass=${echo ${1} |xxd–p}
opensslenc-e -des-ede -nosalt -K ${pass} -iv "0000000000000000" -in ${2} -out ${3}
```

 Run the script with argument

```bash
./encrypt <3DES key> <txt based XML file> <output file, 3des encrypted> <enter>
```

Command to launch firmware upgrade from remote server

!!! Note "Important"
          Must be launched from real shell, type `sh` for get to shell (i have more info about this post the below)

          ```bash
          > tftpbcm -g -t i -f <Firmware file><TFTP server IP>
          ```

## Important files in `/etc`

Configuration files that is really important to keep on device

```
* /etc/build_date
* /etc/default.cfg
* /etc/default.script.sh
* /etc/dhcp6c.conf.sample
* /etc/dhcp6s.conf.sample
* /etc/dms.conf
* /etc/dyndscp.sh
* /etc/filesystems
* /etc/fstab
* /etc/gateway.conf
* /etc/group
* /etc/inetd.conf
* /etc/inittab
* /etc/ipsec.conf
* /etc/ipv6_start.sample
* /etc/mdk
* /etc/model_name
* /etc/modules_install
* /etc/mtab
* /etc/passwd
* /etc/pppmsg
* /etc/profile
* /etc/provision
* /etc/psk.txt
* /etc/racoon.conf
* /etc/radvd.conf.sample
* /etc/resolv.conf
* /etc/samba
* /etc/services
* /etc/soft_bridge
* /etc/sw_version
* /etc/sysmsg
* /etc/udhcpd.conf
* /etc/udhcpd.leases
* /etc/vendor_name
* /etc/vlan
* /etc/vlan.sh
```

## Shell

There is two way to launch commands on this kind of devices, when you will login we gonna end up as we have seen earlier on other devices like Bose Radio Soundtouch and many other devices I have shared how we can pwn on my github, however when we login on our Internet Providers device we gona enter a limited prompt with pre configured commands from the manufacturer but all (so far every device I have hacked) do not limit us to this minimal shell so we can simply take over entire device and enter the real shell as you all know is my best friend, so just type: sh to enter the real shell.

If you are new to working in shell, this is good to know when  you entering a prompt that is NOT edited from default (almost no factory devices is)

### Shell Prompts 


- When you see a simple hash character it mean you are superuser, i.e. uid: `0`

```
# 
```

 - When you see a simple dollar sign, it is typical used for anything NOT uid: `0`
 
 ```
 $
```

- When you see a angel bracket which points to the right means limited shell in most cases, can be anything that has been configured from owner
 
 ```
 > 
 ```

----

## We are in!

```bash
XG6846 Login: 
Password: 
 > sh

BusyBox v1.17.2 (2018-02-01 16:32:22 CST) built-in shell (ash)
Enter 'help' for a list of built-in commands.

#
```

* Screenshot for default commands 

* `/sbin` is _not_ included in `$PATH`

![t](https://user-images.githubusercontent.com/26827453/211178430-9c7fc6fb-e539-4419-8cb5-579358168729.png)

* Video preview 

![Screenshot](hacking-inteno-xg6846/loginPreview.gif)

## Consold

I don't use things that I didn't create or compile from scratch usullay, but since I started with Gentoo I have always gone through all the things that are executable on the device I am investigating, i do them one  by one and trust me it takes LONG time for larger devices to try all commands and all scenarios that happens when executing the commands. What is actually happening on the devices in detail?? What files is created? What files is removed? What files get downloaded after we connect to internet for the first time etc etc..

How else am I supposed to understand if there is no manual that doesn't is in this te.x and if I never used the commands before? So to be the one who controls the device, I also have to know everything that happens and is possible under the router based on the fact that I am the one who controls the device, in this case the internet operator, so here I post all the examples that are possible and all the things that are possible that I don't choose to test before I know exactly what the commands do, te.x "flasherase_all" wont have an example usage, you get it - ;)

So let us go through all these commands in this limited shell one by one (geek warning)


### Command: `help`

```
 > help
- macaddr   
- wanmacaddr  
- serialnum
- wepkey    
- factest   
- ddm_value 
- ddm   
- logout    
- exit    
- quit    
- reboot    
- brctl 
- cat   
- ls    
- loglevel  
- logdest   
- virtualserver 
- df    
- dumpcfg   
- dumpmdm   
- meminfo   
- psp   
- kill    
- dumpsysinfo 
- syslog    
- echo    
- ifconfig  
- ping    
- ps    
- pwd   
- sysinfo   
- tftp  
- arp   
- defaultgateway  
- dhcpserver
- dns   
- lan   
- lanhosts  
- passwd    
- ppp   
- restoredefault  
- route   
- save  
- swversion 
- uptime  
- cfgupdate 
- swupdate  
- exitOnIdle  
- wan
```

### Command: `?`

* Command: `?` will just print available commands and exit

```
> ?
?   help    macaddr   wanmacaddr  
serialnum wepkey    factest   ddm_value 
ddm   logout    exit    quit    
reboot    brctl   cat   ls    
loglevel  logdest   virtualserver df    
dumpcfg   dumpmdm   meminfo   psp   
kill    dumpsysinfo syslog    echo    
ifconfig  ping    ps    pwd   
sysinfo   tftp    arp   defaultgateway  
dhcpserver  dns   lan   lanhosts  
passwd    ppp   restoredefault  route   
save    swversion uptime    cfgupdate 
swupdate  exitOnIdle  wan
```

### Command: `echo` 

echo will just print to stdout but we can use this command for many things if we know how to work in shell wich is not "meant" to be used as this I guess by manufacturer -  Let me go through all this examples if we pretend we didn't know sh command to enter the real shell or in case it ever would be locked in future

### Command: `echo $?` 

Result: print exit status, this command gives us: `0` for all commands executed no matter if its true or false

```
 > echo $?
0
```

### Command: `echo $$`

Result: This command prints current pid we use, this works? This means we are able to do cool stuff I guess, let´s see 

```
 > echo $$ 
17871
 >
```

### Command: `echo $(help)`

This will show us help for default shell in use, this is awesome! 


```bash
echo $(help)
Built-in commands: ------------------ . : alias break cd chdir continue eval exec exit export false hash help let local pwd read readonly return set shift source times trap true type ulimit umask unalias unset wait
```


### Command: `ls and cd`

* ls: it will list current dir only, it's not possible to list any other dir with this limited shell
* cd: we can not use cd to change dir to uss ls to list files in other folders by default


```bash
 > ls         
bin      dev      lib      mnt      proc     sys      usr      webs
data     etc      linuxrc  opt      sbin     tmp      var
 > cd /bin 
consoled:error:93.451:processInput:397:unrecognized command cd /bin
 > ls /bin
consoled:error:95.896:processInput:397:unrecognized command lls /bin
 > 
```

Work around for using ls as in normal shell

```
 > echo $(ls -h) 
ls: invalid option -- h
BusyBox v1.17.2 (2018-02-01 16:32:22 CST) multi-call binary.

Usage: ls [-1AacCdeFilnpLRrSstuvxXk] [FILE]...

List directory contents

Options:
  -1  List in a single column
  -A  Don't list . and ..
  -a  Don't hide entries starting with .
  -C  List by columns
  -c  With -l: sort by ctime
  -d  List directory entries instead of contents
  -e  List full date and time
  -F  Append indicator (one of */=@|) to entries
  -i  List inode numbers
  -l  Long listing format
  -n  List numeric UIDs and GIDs instead of names
  -p  Append indicator (one of /=@|) to entries
  -L  List entries pointed to by symlinks
  -R  Recurse
  -r  Sort in reverse order
  -S  Sort by file size
  -s  List the size of each file, in blocks
  -t  With -l: sort by modification time
  -u  With -l: sort by access time
  -v  Sort by version
  -x  List by lines
  -X  Sort by extension
```

### Command: `echo $(ls -1 /)`

We cant print to stdout as we can in shell, so no commands will work as it should because of this


```bash
 > echo $(ls -1 /)                    
bin data dev etc lib linuxrc mnt opt proc sbin sys tmp usr var webs
```

Some examples for testing without description

```bash
 > echo $(ls -l /bin/)
lrwxrwxrwx 1 admin root 7 Feb 1 2018 ash -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 bash -> busybox lrwxrwxrwx 1 admin root 6 Feb 1 2018 bpm -> bpmctl -rwxr-xr-x 1 admin root 9424 Feb 1 2018 bpmctl -rwxr-xr-x 1 admin root 39968 Feb 1 2018 brctl -rwxr-xr-x 1 admin root 333640 Feb 1 2018 busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 cat -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 chmod -> busybox -rwxr-xr-x 1 admin root 6308 Feb 1 2018 consoled lrwxrwxrwx 1 admin root 7 Feb 1 2018 cp -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 date -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 deluser -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 df -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 dmesg -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 echo -> busybox -rwxr-xr-x 1 admin root 12640 Feb 1 2018 ethctl lrwxrwxrwx 1 admin root 7 Feb 1 2018 false -> busybox lrwxrwxrwx 1 admin root 5 Feb 1 2018 fc -> fcctl -rwxr-xr-x 1 admin root 9176 Feb 1 2018 fcctl lrwxrwxrwx 1 admin root 7 Feb 1 2018 grep -> busybox -rwxr-xr-x 1 admin root 10820 Feb 1 2018 hotplug -rwxr-xr-x 1 admin root 260128 Feb 1 2018 httpd -rwxr-xr-x 1 admin root 207976 Feb 1 2018 ip lrwxrwxrwx 1 admin root 5 Feb 1 2018 iq -> iqctl -rwxr-xr-x 1 admin root 11808 Feb 1 2018 iqctl lrwxrwxrwx 1 admin root 7 Feb 1 2018 kill -> busybox -rwxr-xr-x 1 admin root 7104 Feb 1 2018 ledctl lrwxrwxrwx 1 admin root 7 Feb 1 2018 ln -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 ls -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 mkdir -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 mknod -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 mount -> busybox -rwxr-xr-x 1 admin root 5192 Feb 1 2018 multimac lrwxrwxrwx 1 admin root 7 Feb 1 2018 ping -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 ping6 -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 ps -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 pwd -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 rm -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 sh -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 sleep -> busybox -rwxr-xr-x 1 admin root 62164 Feb 1 2018 smd -rwxr-xr-x 1 admin root 73636 Feb 1 2018 ssk -rwxr-xr-x 1 admin root 21476 Feb 1 2018 stress lrwxrwxrwx 1 admin root 7 Feb 1 2018 stty -> busybox -rwxr-xr-x 1 admin root 18492 Feb 1 2018 sw_upgrade -rwxr-xr-x 1 admin root 232196 Feb 1 2018 tc lrwxrwxrwx 1 admin root 7 Feb 1 2018 true -> busybox lrwxrwxrwx 1 admin root 7 Feb 1 2018 umount -> busybox -rwxr-xr-x 1 admin root 13064 Feb 1 2018 xavi_switch_daemon -rwxr-xr-x 1 admin root 14488 Feb 1 2018 xavi_wan
```

### Workaround that works for real to list files as expected

```bash
 > echo $(busybox "$(ls -1)")
bin
data
dev
etc
lib
linuxrc
mnt
opt
proc
sbin
sys
tmp
usr
var
webs: applet not found

```

More commands that works and not works (no descriptions)

* This examples does NOT work

```bash
> echo $(busybox $(ls -l /))
drwxrwxr-x: applet not found
```
```bash
 > echo $(busybox '$(ls -l /)')
): applet not found
```

```bash
 > echo $(busybox '$(ls -l /)')
```



> It is required to use double quotes for execute commands correct in this limtied shell <br>
{: .prompt-info }

### However, for reverse shell  we can launch below command

```bash
echo $(busybox nc -lvvp 1337)
```

Let us move to next command in this limited shell (consold)

### Command: `serialnum`

Will just print device serial number

```bash
FG5024H131007601
```

### Command: `macaddr/wanmacaddr`

* Will just print hw address without colons

```bash
0022072B0A5D
```


### Command: `wepkey`

* Will not print anything since we don't have wifi pass on this one otherwise it print wepkey on INteno devices and they use 10 characters with uppercase letters + numbers in their default wepkeys

```bash
3A2451CC4E
```


### Command: `ddm`

* Will dump _all_ settings on device

```bash
....to long
```


### Command: `loglevel`

It is possible to set log by appname, options: httpd, tr69c, smd, ssk, telnetd, sshd, consoled, upnp, dnsproxy, vodsl, dectd, snmpd

* Set loglevel for an app 

```bash
loglevel set appname loglevel
```

* Get loglevel for an app

```bash
loglevel get appname
```

### Command: `logdest`

Log destination must be one of: stderr, syslog or telnet and same options as above for this command

```bash
logdest set appname logdes
```


### Command: `virtualserver`

* Show current virtual server in use

```bash
virtualserver show
```

* Enable/Disable virtualserver

```bash
virtualserver enable|disable <num>
```

### Command: `dumpcfg`

* Will dump default.cfg from /etc/defauult.cfg

```bash
 > dumpcfg
<?xml version="1.0"?>
<DslCpeConfig version="3.0">
  <InternetGatewayDevice>
    <LANDeviceNumberOfEntries>1</LANDeviceNumberOfEntries>
    <WANDeviceNumberOfEntries>1</WANDeviceNumberOfEntries>
    <DeviceInfo>
      <ProvisioningCode>12345</ProvisioningCode>
      <FirstUseDate>2011-05-13T05:07:13+00:00</FirstUseDate>
      <VendorConfigFileNumberOfEntries>0</VendorConfigFileNumberOfEntries>
    </DeviceInfo>
.........    
```


### Command: `dumpmdm`

* Will dump all settings that is set in firmware and pint allocated settings and how man that are in use:

dump bytes allocated=98304 used=41880 > meminfo

```bash
dumpmdm
```


### Command: `meminfo`

Will print total MDM Shared memory Region of device

```bash

```


### Command: `meminfo`


```bash
Total MDM Shared Memory Region : 416KB
Shared Memory Usable           : 000335KB
Shared Memory in-use           : 000075KB
Shared Memory free             : 000260KB
Shared Memory allocs           : 6822412
Shared Memory frees            : 6820966
Shared Memory alloc/free delta : 001446

Heap bytes in-use     : 000016
Heap allocs           : 000842
Heap frees            : 000841
Heap alloc/free delta : 000001

```


### Command: `psp`


```bash

```bash
psp list
```

```bash
psp dump xxx
```

```bash
psp delete xxx
```

```bash
psp clearall
```



### Command: `syslog`

* Dump syslog - Its disable so nothing will be printed

```bash
syslog dump
==== Dump of Syslog ====
```

### Command: `sysinfo`

* Wil print uuptime and memory usage from `free -h`

```bash
 > sysinfo
Number of processes: 84
  7:31pm  up 1 day, 16:24, 
load average: 1 min:2.32, 5 min:2.44, 15 min:2.44
              total         used         free       shared      buffers
  Mem:        59196        53316         5880            0         1740
 Swap:            0            0            0
Total:        59196        53316         5880

```


### Command: `defaultgateway`

```bash

```


### Command: `dhcpserver`

Configure dhcp server 

```bash
dhcpserver config <start IP address> <end IP address> <leased time (hour)>
```

* Show current dhcpcd usage

```bash
 > dhcpserver show
dhcpserver: enable
start ip address: 192.168.1.2
end ip address: 192.168.1.254
leased time: 24 hours
```

### Command: `dns`

* Configure static dns

```bash
dns config static [<primary DNS> [<secondary DNS>]]
```

* Dump stratus for dns

```bash
dns show
```


### Command: `lan`

Configure lan config

```bash
lan config [--ipaddr <primary|secondary> <IP address> <subnet mask>]
                  [--dhcpserver <enable|disable>]
                  [--dhcpclient <enable|disable>]
```

* Delete lan

```bash
lan delete --ipaddr <secondary>
```

* Dump lan settings

```bash
lan show <primary |secondary>
```

### Command: `lanhosts`

* Show all hosts on lan 

```bash
lan show all
```

* Show all'lan hosts connected on interface

```bash
lan show br0
```

### Command: `passwd`

* Change password of user (is not available since / is mounted in ro and we cant remount it as rw)

```bash
passwd
Username: root
Password:  chang3m4
Again: chang3m4

```

### Command: `ppp`

* Disconnect or connect ppp

```bash
ppp config <ppp interface name (eg. ppp0)> <up|down>
```

### Command: `restoredefault`

self explanatory but this will complete reset to factory default settings



### Command: `route`

* Add route

```bash
route add <IP address> <subnet mask> |metric hops| <|<gw gtwy_IP>| |<dev interface>
```

* Delete route

route delete <IP address> <subnet mask>

* Show route

```bash
route show
```


## Shell

### `swversion`

* Show sw version

```bash
4.10ITT02.56
```

### `/proc/version`

```bash
Linux version 2.6.30 (hank_chiang@cs1) (gcc version 4.4.2 (Buildroot 2010.02-git) ) #2 Thu Feb 1 16:36:37 CST 2018
```

### `/proc/brcm/kernel_config`


```bash
CONFIG_SMP=0
CONFIG_PREEMPT=0
CONFIG_DEBUG_SPINLOCK=0
CONFIG_DEBUG_MUTEXES=0

```


### `/proc/brcm/softirq_in_kthread`


```bash
1
```


### `/proc/buddyinfo`


```bash
Node 0, zone      DMA      1      2      2      1      2      1      1      0      0      0      3 
Node 0, zone   Normal      3      5      2      2      3      2      2      3      2      1      5
```


### `/proc/cmdline`


```bash
root=31:0 ro noinitrd console=ttyS0,115200
```


### `/proc/crypto`


```bash
name         : stdrng
driver       : krng
module       : kernel
priority     : 200
refcnt       : 1
selftest     : passed
type         : rng
seedsize     : 0

name         : aes
driver       : aes-generic
module       : kernel
priority     : 100
refcnt       : 1
selftest     : passed
type         : cipher
blocksize    : 16
min keysize  : 16
max keysize  : 32

name         : des3_ede
driver       : des3_ede-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : cipher
blocksize    : 8
min keysize  : 24
max keysize  : 24

name         : des
driver       : des-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : cipher
blocksize    : 8
min keysize  : 8
max keysize  : 8

name         : sha1
driver       : sha1-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : shash
blocksize    : 64
digestsize   : 20

name         : md5
driver       : md5-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : shash
blocksize    : 64
digestsize   : 16

name         : compress_null
driver       : compress_null-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : compression

name         : digest_null
driver       : digest_null-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : shash
blocksize    : 1
digestsize   : 0

name         : ecb(cipher_null)
driver       : ecb-cipher_null
module       : kernel
priority     : 100
refcnt       : 1
selftest     : passed
type         : blkcipher
blocksize    : 1
min keysize  : 0
max keysize  : 0
ivsize       : 0
geniv        : <default>

name         : cipher_null
driver       : cipher_null-generic
module       : kernel
priority     : 0
refcnt       : 1
selftest     : passed
type         : cipher
blocksize    : 1
min keysize  : 0
max keysize  : 0

```


### `/proc/devices`


```bash
Character devices:
  1 mem
  2 pty
  3 ttyp
  4 ttyS
  5 /dev/tty
  5 /dev/console
  5 /dev/ptmx
 10 misc
108 ppp
128 ptm
136 pts
180 usb
189 usb_device
206 brcmboard
242 fcache
243 ingqos
244 bpm
246 chipinfo
254 usb_endpoint

Block devices:
259 blkext
  8 sd
 31 mtdblock
 65 sd
 66 sd
 67 sd
 68 sd
 69 sd
 70 sd
 71 sd
128 sd
129 sd
130 sd
131 sd
132 sd
133 sd
134 sd
135 sd

```


### `cat /proc/diskstats`

```bash
  31       0 mtdblock0 59 706 1530 174 0 0 0 0 0 174 174
```


### `/proc/driver/bcmsw/dma_summary `

```bash
== dma controller registers ==
controller config: 00000003
ch:  config:int stat:int mask
rx:00000001:00000000:00000007
tx:00000000:00000007:00000000

== sram contents ==
ch: bd base:  status:current bd content
rx:038a8000:00000084:06002000:03a64250
tx:03972000:00000011:003c6110:02982802

== MIPS and MISC registers ==
CP0 cause:        00008000
CP0 status:       10000401

```


### `cat /proc/driver/mvsw/speed `

```bash
Port 0: 1000 Mbps, Full-duplex
Port 1: Link is down
Port 2: Link is down
Port 3: Link is down
Port 4: Link is down
Port 5: 1000 Mbps, Full-duplex
Port 6: 1000 Mbps, Full-duplex
```


### `/proc/driver/mvsw/linkstatus`

```bash
100001
```


### `/proc/driver/mvsw/mac_list `

```bash
14:57:9F:9D:F8:60 FID=00 State=06 P-5
78:BA:F9:42:B0:1A FID=00 State=06 P-5
78:BA:F9:42:B0:1B FID=00 State=07 P-5
AC:75:1D:BE:F1:70 FID=00 State=06 P-5
E0:B9:E5:1D:F1:B2 FID=00 State=06 P-0
E0:B9:E5:1D:F1:B3 FID=00 State=06 P-0
E2:B9:E5:1D:F1:B2 FID=00 State=06 P-0

```


### `/proc/driver/mvsw/pav`

```bash
1
2
4
8
16
32
64
```


### `/proc/driver/mvsw/port_link`

```bash
1
```


### `/proc/driver/mvsw/reg`

```bash
0x0003
```

### `/proc/driver/mvsw/reset`

```bash
0001
```


### `/proc/driver/mvsw/tline`

```bash
 296:3333321
 845:2133321
 855:2313321
 865:3331321
 875:3333121
 865:3331321
 875:3333121
```


### `/proc/execdomains`

```bash
0-0 Linux             [kernel]
```


### `/proc/fcache/brlist `

```bash
Broadcom Packet Flow Cache v2.2 Apr  3 2012 16:29:45
FlowObject idle:+swhit SW_TotHits:TotalBytes CMFtpl HW_TotHits V4Conntrack V6Conntrack L1-Info Prot   SourceIpAddress:Port    DestinIpAddress:Port    VlanID(mcast)   tag#   IqPrio    SkbMark
```


### `/proc/fcache/nflist`

```bash
Broadcom Packet Flow Cache v2.2 Apr  3 2012 16:29:45
FlowObject idle:+swhit SW_TotHits:TotalBytes CMFtpl HW_TotHits V4Conntrack V6Conntrack L1-Info Prot   SourceIpAddress:Port    DestinIpAddress:Port    VlanID(mcast)   tag#   IqPrio    SkbMark
```

### `/proc/filesystems`

```bash
nodev sysfs
nodev rootfs
nodev bdev
nodev proc
nodev sockfs
nodev usbfs
nodev pipefs
nodev tmpfs
nodev devpts
  ext3
  squashfs
nodev ramfs
  vfat
nodev fuse
  fuseblk
nodev fusect
```


### `/proc/interrupts`

```bash
           CPU0       
 19:          0  BCM63xx_no_unmask     brcm_19(0x60)
 20:          0  BCM63xx_no_unmask     brcm_20(0x60)
 33:          0  BCM63xx_no_unmask     brcm_33(0x60)
 36:       6439  BCM63xx_no_unmask     serial(0x60)
 39:    1331201  BCM63xx               Periph Timer(0xa0)
 40:        165  BCM63xx_no_unmask     brcm_40(0x60)
 41:          0  BCM63xx_no_unmask     brcm_41(0x60)
 42:          0  BCM63xx_no_unmask     brcm_42(0x60)
 43:          0  BCM63xx_no_unmask     brcm_43(0x60)
 49:          1  BCM63xx               ohci_hcd:usb2(0x80)
 50:          0  BCM63xx               ehci_hcd:usb1(0x80)

ERR:          0
```


### `/proc/iomem`

```bash
00000000-03efffff : System RAM
  00010000-0028c5cf : Kernel code
  0028c5d0-003355ff : Kernel data
10002500-100025ff : ehci_hcd
10002600-100026ff : ohci_hcd
10f00000-10ffffff : bcm63xx pcie memory space
11000000-11ffffff : bcm63xx pci memory space
```


### `/proc/ioports`

```bash
13000000-1300ffff : bcm63xx pci IO space
```

!!! Note "Important"
          Commands listed below is now described and will be added later..
          ```bash
          irq
          kallsyms
          kcore
          kmsg
          kpagecount
          kpageflags
          led
          loadavg
          locks
          meminfo
          mii
          misc
          modules
          mounts
          mtd
          net
          nvram
          pagetypeinfo
          partitions
          scsi
          self
          slabinfo
          stat
          switch
          sys
          sysrq-trigger
          sysvipc
          timer_list
          tty
          uptime
          version
          vmallocinfo
          vmstat
          xavi_catv_alarm
          xavi_fiber
          xavi_led_mode
          xavi_port_limit
          zoneinfo
          ```

## sysreq

### `^h`

```bash
SysRq : HELP : loglevel(0-9) BRCM: show summary status on all CPUs(A) reBoot terminate-all-tasks(E) memory-full-oom-kill(F) kill-all-tasks(I) thaw-filesystems(J) show-backtrace-all-active-cpus(L) show-memory-usage(M) nice-all-RT-tasks(N) show-registers(P) show-all-timers(Q) Sync show-task-states(T) Unmount show-blocked-tasks(W) BRCM: show interrupt counts on all CPUs(Y) 
All commands are case insensitive.
```

### `^M`

```bash
SysRq : Show Memory
Mem-Info:
DMA per-cpu:
CPU    0: hi:    0, btch:   1 usd:   0
Normal per-cpu:
CPU    0: hi:    6, btch:   1 usd:   5
Active_anon:409 active_file:399 inactive_anon:0
 inactive_file:631 dirty:0 writeback:0 unstable:0
 free:10045 slab:3424 mapped:379 pagetables:51 bounce:0
DMA free:12884kB min:256kB low:320kB high:384kB active_anon:0kB inactive_anon:0kB active_file:0kB inactive_file:0kB present:16256kB pages_scanned:0 all_unreclaimable? no
lowmem_reserve[]: 0 46 46
Normal free:27296kB min:752kB low:940kB high:1128kB active_anon:1636kB inactive_anon:0kB active_file:1596kB inactive_file:2524kB present:47752kB pages_scanned:0 all_unreclaimable? no
lowmem_reserve[]: 0 0 0
DMA: 1*4kB 2*8kB 2*16kB 1*32kB 2*64kB 1*128kB 1*256kB 0*512kB 0*1024kB 0*2048kB 3*4096kB = 12884kB
Normal: 12*4kB 8*8kB 5*16kB 1*32kB 1*64kB 3*128kB 2*256kB 3*512kB 2*1024kB 1*2048kB 5*4096kB = 27296kB
1030 total pagecache pages
16128 pages RAM
1017 pages reserved
1785 pages shared
1629 pages non-shared
```


### `^L`

```bash
SysRq : Show backtrace of all active CPUs
=>entered showacpu on CPU 0: (idle=0)
=>calling show_regs:
Cpu 0
$ 0   : 00000000 10000401 80000000 80000000
$ 4   : 00000000 80002000 00008000 00000000
$ 8   : 00002000 00000000 00000002 80334618
$12   : 00000248 0000000c 0000021c 00000001
$16   : 2aaad000 829e5908 00400000 2aabe000
$20   : ffffffbf 829e5908 829f2b60 8399d1c4
$24   : 000002c8 8001d5b0                  
$28   : 829fc000 829ffce0 00000000 800779ac
Hi    : 00000000
Lo    : 000002c8
epc   : 8001d640 blast_icache16+0x90/0xec
    Tainted: P          
ra    : 800779ac unmap_vmas+0x558/0x63c
Status: 10000403    KERNEL EXL IE 
Cause : 00008400
PrId  : 0002a075 (Broadcom4350)
CPU0:
=>calling show_stack, current=829f9a78  (comm=[)
Stack : 829ffa68 00000004 80360000 0000003c 80320000 80033448 00000001 00000068
        0000004c 00000020 80360000 80360000 802d0000 00000020 10000400 829ffcb0
        802d2f30 00000020 829ffc30 00008400 802d0000 00000020 00000021 829ffcb0
        802d2f30 00000020 802e48d8 80289528 10000400 80350000 ffffffff 0000002a
        10000400 8396d000 80329114 80320000 10000400 00000008 0000005e 829ffa38
        ...
Call Trace:(--Raw--
[<80033448>] vprintk+0x338/0x428
[<80289528>] printk+0x24/0x30
[<8016b33c>] sysrq_handle_showallcpus+0xd0/0x114
[<80018f10>] show_stack+0x64/0x84
[<8016b33c>] sysrq_handle_showallcpus+0xd0/0x114
[<8016aef8>] __handle_sysrq+0xd0/0x248
[<801cca84>] bcm63xx_int+0x2e4/0x380
[<8005dff8>] handle_IRQ_event+0xb0/0x4e0
[<80060528>] handle_level_irq+0x7c/0x114
[<800116bc>] plat_irq_dispatch+0x160/0x254
[<800116bc>] plat_irq_dispatch+0x160/0x254
[<800125e4>] ret_from_irq+0x0/0x4
[<8001d5b0>] blast_icache16+0x0/0xec
[<800779ac>] unmap_vmas+0x558/0x63c
[<8007c450>] __vma_link+0x38/0x84
[<8001d640>] blast_icache16+0x90/0xec
[<8006fdfc>] ____pagevec_lru_add+0x1b0/0x200
[<80074288>] vma_prio_tree_insert+0x28/0x60
[<8007bc28>] unmap_region+0x94/0x128
[<8007cfbc>] do_munmap+0x284/0x330
[<800bd4c4>] elf_map+0x188/0x190
[<800bd07c>] padzero+0x70/0x84
[<800be518>] load_elf_binary+0x104c/0x1434
[<8008e714>] search_binary_handler+0xb8/0x2f4
[<8008faf8>] do_execve+0x230/0x338
[<80092c04>] getname+0xa4/0x140
[<80016f0c>] sys_execve+0x44/0x74
[<8001a390>] stack_done+0x20/0x3c


Call Trace:
[<80018f10>] show_stack+0x64/0x84
[<8016b33c>] sysrq_handle_showallcpus+0xd0/0x114
[<8016aef8>] __handle_sysrq+0xd0/0x248
[<801cca84>] bcm63xx_int+0x2e4/0x380
[<8005dff8>] handle_IRQ_event+0xb0/0x4e0
[<80060528>] handle_level_irq+0x7c/0x114
[<800116bc>] plat_irq_dispatch+0x160/0x254
[<800125e4>] ret_from_irq+0x0/0x4
[<8001d640>] blast_icache16+0x90/0xec
[<800779ac>] unmap_vmas+0x558/0x63c
[<8007bc28>] unmap_region+0x94/0x128
[<8007cfbc>] do_munmap+0x284/0x330
[<800bd4c4>] elf_map+0x188/0x190
[<800be518>] load_elf_binary+0x104c/0x1434
[<8008e714>] search_binary_handler+0xb8/0x2f4
[<8008faf8>] do_execve+0x230/0x338
[<80016f0c>] sys_execve+0x44/0x74
[<8001a390>] stack_done+0x20/0x3c

```


### `^Q`

```bash
SysRq : Show clockevent devices & pending hrtimers (no others)
Timer List Version: v0.4
HRTIMER_MAX_CLOCK_BASES: 2
now at 1586628740067 nsecs

cpu: 0
 clock 0:
  .base:       8031b660
  .index:      0
  .resolution: 999848 nsecs
  .get_time:   ktime_get_real
active timers:
 clock 1:
  .base:       8031b688
  .index:      1
  .resolution: 999848 nsecs
  .get_time:   ktime_get
active timers:
 #0: <8035a6e0>, sched_rt_period_timer, S:01
 # expires at 1587000000000-1587000000000 nsecs [in 371259933 to 371259933 nsecs]
 #1: <8395be98>, hrtimer_wakeup, S:01
 # expires at 1587118307229-1587118357229 nsecs [in 489567162 to 489617162 nsecs]
 #2: <82993ad0>, hrtimer_wakeup, S:01
 # expires at 1587299653509-1587301653504 nsecs [in 670913442 to 672913437 nsecs]
 #3: <838b7e98>, hrtimer_wakeup, S:01
 # expires at 1587790306089-1587790356089 nsecs [in 1161566022 to 1161616022 nsecs]
 #4: <838c3a38>, hrtimer_wakeup, S:01
 # expires at 1599110277029-1599110277029 nsecs [in 12481536962 to 12481536962 nsecs]
 #5: <829dba38>, hrtimer_wakeup, S:01
 # expires at 3139135994869-3139136044869 nsecs [in 1552507254802 to 1552507304802 nsecs]
 #6: <83953e98>, hrtimer_wakeup, S:01
 # expires at 86492321771389-86492321821389 nsecs [in 84905693031322 to 84905693081322 nsecs]
 #7: <83867a38>, hrtimer_wakeup, S:01
 # expires at 4295220102580349-4295220102630349 nsecs [in 4293633473840282 to 4293633473890282 nsecs]


Tick Device: mode:     0
Per CPU device: 0
Clock Event Device: BCM Periph Timer
 max_delta_ns:   2147483647
 min_delta_ns:   15360
 mult:           214748364
 shift:          32
 mode:           3
 next_event:     1586621000000 nsecs
 set_next_event: bcm_mips_next_event0
 set_mode:       bcm_mips_set_clock_mode
 event_handler:  tick_handle_periodic
```
