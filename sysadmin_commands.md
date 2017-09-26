# kill -9 `ps aux | awk '$1=="chavez" {print $2}'
$ write chavez
# cd /etc
# cp inittab inittab.dist # chmod a-w inittab.dist
$ su
$ su -
# passwd Works most of the time.
# passwd root Solaris and FreeBSD systems when su’d to root.
# lsuser -a sugroups root
# chuser sugroups="system,admins" root
$ su root -c "eject /cdrom" Password: root password entered
# cd sudo-source-directory
# ./configure --with-logpath=/var/log/sudo.log \
$ write username [tty]
$ write chavez@hamlet
$ wall
$ smit -s /dev/null -l /dev/null ...
#egrep '^task [a-z]|^ *execute' pm.tm task pm_get_ps {
$ plod [text]
$ plod -g hp-ux
$ ls -l
# chown new-owner files
# chown harvey brass
# chown -R harvey /home/iago/new/tgh
# chown new-owner:new-group files
$ rm copper
$ ls -l
$ chmod u+w lead
$ chmod a+w lead
$ chmod a-w lead
$ chmod a=r lead
$ chmod u+w,og+r-w bronze brass
$ chmod -R go-rwx /home/chavez Beyond the basics
$ chmod -R +r .
$ chmod go= lead
$ ls -lF -rw------- drwx------ -rwx------
$ chmod go+rX * $ ls -lF -rw-r--r-- drwxr-xr-x -rwxr-xr-x
$ chmod o=g *
$ chmod 754 pewter Specifying the default file mode
$ umask 023
$ umask Displays the current value. 23
$ ls -l gold silver
$ chmod +rwx gold
$ chmod -rwx silver
$ ls -l gold silver -rwxr-xr-- 1 chavez chem -----w--wx 1 chavez chem
# chmod u+t /tmp
$ ls -ld /tmp
# chmod g+s /pub/chem2
# chmod 4755 # chmod 2755 # chmod 6755 # chmod 1777 # chmod 2745 # ls -ld -rwsr-sr-x -rwxr-sr-x -rwxr-Sr-x drwxrwxrwt -rwsr-xr-x
$ ln index hlink
$ pwd; cd ./htdocs
$ cd ../bin
$ ls -l /home/chavez/htdocs
$ cd /home/chavez/htdocs/../cgi-bin; pwd /public/web2/apache/cgi-bin
$ ls -lF /var/adm/c*
# cd /var/adm
# mkcdsl pacct
# ls -l pacct
# ln -s "../cluster/members/{memb}/adm/pacct" ./pacct
# file *
$ long_cmd & Stop foreground process.
$ long_cmd ^Z Stopped $
$ jobs
$ fg %1
$ fg %?em
$ long_cmd
$ bg
$ od data.raw
# /usr/sbin/ping hamlet
$ cd /dev; ls -l *mouse
# mount /dev/disk0a /
# mount /dev/disk1e /home
$ man umount fsck newfs
$ man -a intro $ man -a chown
# makewhatis Most systems # makewhat /usr/share/man Solaris
$ ps -ef | grep "[q]uake" | awk '{print $1}'
$ (date ; ps -ef | grep "[q]uake" | awk '{print $1 " [" $7 "]"}' \ | sort | uniq) >> quaked.users
# find / -user chavez -fstype 4.2 ! -name /dev/\* -ls | \
$ sys_doc > `date | awk '{print $3 $2 $6}'`.`hostname`.sysdoc
$ date +junk_%d%b%y%H%M%S.junk junk_08Dec01204256.junk
# find starting-dir(s) matching-criteria-and-actions
$ find . -name \*.c -print
$ find /chem -size +2048 -mtime +30 -exec ls -l {} \;
$ find /chem -size +2048 \( -mtime +30 -o -atime +120 \) -ls
# find / \( -name a.out -o -name core -o -name '*~'\
# find /home /aux1 -type f -atime +30 -size +1000 -print \ -name core -exec rm {} \;
# find / -type f \( -perm -2000 -o -perm -4000 \) -print
# find / \( -perm -2000 -o -perm -4000 \) -print | \ diff - files.secure
# find /home/chavez -exec chown chavez {} \; \ -exec chgrp physics {} \;
# find /chem -name '*.c' -exec mv {} /chem1/src \;
# find /chem -name '*.c' -exec /usr/local/bin/prettify {} \;
# touch /tmp/starting_time
# perform some operation
# find / -newer /tmp/starting_time
# ps -ef | grep "[q]uake" | awk '{print $2}' | xargs renice +10
# ps -ef | grep "[q]uake" | awk '{print $1}' | xargs -n1 warn_user
# ps -ef | grep "[q]uake" | awk '{print $1}' | \ xargs -i chargefee {} 10000
$ echo a b c d e f | xargs -n3 -i echo before {} after before a b c d e f after
$ echo a b c d e f | xargs -i -n3 echo before {} after before {} after a b c
$ tail test00* | more Onmostsystems,thiscommanddisplayslinesonlyfromthelastfile.However,Ican
$ ls -1 test00* | xargs -i /usr/bin/sh -c \
$ ls *.dat | xargs -i /usr/bin/sh -c "sort -u -o {} {}" Creating Several Directory Levels at Once
$ mkdir -m 755 ./people
$ mkdir -m u=rwx,go=rx ./places
$ mkdir -m g+w ./things
$ mkdir -p ./a/b/c
# cd /chem1
# tar -cf - -C /chem olddir | tar -xvpf - # mv olddir newdir
# mkdir /chem1/newdir
# cd /chem1/olddir
# tar -cvf - src bin data logfile.* .profile |\
# mkdir /chem1/newdir
# cd /chem1/newdir
# tar -cvf - -C /chem/olddir src/viewers/rasmol | tar -xvpf -
# mkdir /chem1/newdir
# find . -print | cpio -pdvm /chem1/newdir
# cp -pr /chem/olddir /chem1
# mv /chem1/olddir /chem1/newdir
$ dircmp /chem/olddir /chem1/newdir
# chroot /jail sendmail -bd -q10m
$ tar -cvf /dev/rmt1 /chem /chem1 > 24oct94_tar.toc & $ tail -f 24oct94_tar.toc
$ echo *
$ crontab mycron
$ crontab -l >mycron $ vi mycron
$ crontab mycron
$ crontab -e
# crontab -e adm
# crontab -e -u adm
# daily general settings
# logger -p auth.alert -t DOT_FILE_CHK \ "$user's $file is world-writeable"
$ dmesg | egrep 'down|up'
#       Description: Cause (Solution)
# errclear 14
# /usr/lib/errdemon -l
# dia | egrep '^(Event seq)|(Entry typ)|(ASCII Mes.*[a-z])' Event sequence number 10.
# dia -e s:13 e:13
# global settings
# Syntax:
# event
#
# network events
#
# swatch -c /etc/swatch.config -t /var/adm/messages
# pkginfo -l SUNWbzip PKGINST: SUNWbzip
# pkgchk -l SUNWbzip | grep ^Pathname: | awk '{print $2}' /usr Subdirectories in the package are created on /usr/bin install if they do not already exist. /usr/bin/bunzip2
# pkgchk -l -p /etc/syslog.conf Pathname: /etc/syslog.conf Type: editted file
# pkgadd -d /cdrom/s8-software-companion/components/sparc/Packages SFWgcc Removing an installed package is also very simple:
# pkgrm SFWbzip
# swlist -s /dev/rmt/0m
# swlist -l product
# swlist -l subproduct MSDOS-Utils
# MSDOS-Utils
# lslpp -l all | grep -i compil
# installp -ac -d device [items | all]
# cd /usr/ports/security/tripwire # make && make install
$ gunzip mtools-3.9.7.tar.gz
$ tar xvf mtools-3.9.7.tar
$ cd mtools-3.9.7; ls
$ ./configure
$ more Makefile
# Generated automatically from Makefile.in by configure. # Makefile for Mtools
$ su
# make -n install Preview first!
# make install Proceed if it looks ok. ./mkinstalldirs /usr/local/bin
$ make
# make install PREFIX=/usr/local
# Select the right libraries for your system.
#LIBS = -lcurses
# define a symbol for your OS if it hasn’t got one. See sysdep.h. SYSDEFS = -DHPUX -Ac –Ac says to use the K&R Edition 1 version of C.
# ^D Continue boot process to multiuser mode. Tue Jul 14 14:47:14 EDT 1987 Boot messages from the initialization files. ...
# bootlist -m normal cd1 hdisk0 hdisk1 rmt0
# alog -f /var/adm/ras/bootlog -o
# /sbin/fsck -p /dev/disk2e
# ^D
# mount -o rw,remount /
# mount -u -o rw /
$ who -r
# telinit 6
# cd /sbin
# ln init telinit
# set default init level -- multiuser mode with networking
$ ls -C /etc/rc2.d
# and then the S-files:
#
#
# cp -p /etc/rc /etc/rc.orig
# cp -p /etc/rc.local /etc/rc.local.orig # chmod a-w /etc/rc*.orig
# cd /etc
# mv rc rc.orig; cp rc.orig rc
# mv rc.local rc.local.orig; cp rc.local.orig rc.local # chmod a-w rc.orig rc.local.orig
# shutdown -y -g 15 -i s "system going down" Solaris # shutdown -y 15 HP-UX
# shutdown -y -g 0 -i 6 "system reboot" Solaris # shutdown -y -r now HP-UX
# shutdown +60 "System going down for regular maintenance"
# shutdown -h -t 5 now
# sync # sync
# ps -ax | grep shutdown BSD-style
# ps -ef | grep shutdown System V–style
# ps -ef | grep shutdown
# kill -9 25723
# echo /etc/rc*
# . /etc/rc.dist
# ifconfig lo0 localhost up
# ifconfig eth0 inet 192.168.1.9 netmask 255.255.255.0
# ifconfig tu0 192.168.9.6/24 Ethernet interface names
$ ifconfig eth0
$ ifconfig -l en0 en1 lo0
# ifconfig hme0 plumb
# ifconfig hme0 inet 192.168.9.2 netmask + up
# ifconfig hme0 dhcp
# route add -net 192.168.2.0 netmask 255.255.255.0 192.168.3.100
# route add -net 192.168.2.0 -netmask 255.255.255.0 192.168.3.100 Linux uses a slightly different form for the route command:
# route add -net 10.1.2.0 netmask 255.255.240.0 gw 10.1.3.100 The gw keyword is required.
# netstat -r Routing Table: IPv4
# route delete -net 192.168.1.0 netmask 255.255.255.0 192.168.2.100
$ ping hamlet
$ ping duncan duncan is alive
# arp -a
# cd /etc
# cp passwd passwd.sav Save a copy of the current file
# chmod go= passwd.sav Protect the copy (or use a umask that does this) # emacs passwd
$ groups chavez users bio
$ newgrp chem
$ id
$ setgroups -a phys,bio
$ setgroups -r phys
$ setgroups -d phys
$ setgroups
# ls -l /etc/pass* /etc/group* /etc/*shad*
# chsh -s /bin/tcsh chavez
# passwd chavez
# mkdir /home/chavez
# chown chavez.chem /home/chavez # chmod 755 /home/chavez
# cp /etc/skel/.bash* /home/chavez
# cp /etc/skel/.log{in,out} /home/chavez # cp /etc/skel/.tcshrc /home/chavez
# chown chavez.chem /home/chavez/.[a-z]*
# sample .login file
# suppress core files
# set default umask
# enable messages via write
# enable new mail messages
# add items to the system path
# set an application-specific environment variable
# default printer
# preferred editor
# make more always clear screen
# sample .cshrc file
# the next alias shows the method for including a replaceable
# command line parameter within an alias definition: \!:1 => $1
# set shell variables to specified various features
# remember 100 commands
# save 100 commands across logins
# never beep!
# logout after 1 hour idle time
# warn about overwriting files
# don't interpret ^D as logout
# define some aliases
# try to correct mistyped commands
# 2 ^D's => logout
# confirm rm * commands
# prompt is: hostname:dir-cmd_num>>
# remove id alias
# chown -R chavez:chem /home/chavez
# su - harvey ******************************************************* ** Regular Maintenance from 20:00 - 23:00 today ** ******************************************************* harvey@phoenix /home/harvey>> clear
# useradd -g chem -G bio,phys -s /bin/tcsh -c "Rachel Chavez" -m chavez
# useradd normal options -x logon_hours=Wk0900-1700 chavez
# useradd -D -g chem -b /abode -f -1
# usermod -m -l vasquez chavez
# chsh -s /bin/ksh harvey
# chfn -f "Harvey Thomas" -o 220 -p 555-9876 -h 555-1234 harvey
# chfn -f "Harvey Thomas" -r 220 -w 555-9876 \ -h 555-1234 harvey
# userdel chavez
# groupadd socio
# gpasswd -a chavez drama
# gpasswd -A root,nielsen bio
# adduser -s
#Changing user database information for zelda.
# rmuser zelda
# mkuser home=/home/chavez gecos="Rachel Chavez" pgrp=chem chavez
#!/bin/csh
# rmuser -p chavez
# mkgroup users=lasala,yale,cox,dubail webart
# no username
# send mail unless told not to
# save username
# force password change
# loop over options
# process each option
# don't send mail
$ echo cerise xyzzy | spell -l xyzzy
$taRTra#
# passwd -n 7 -x 365 chavez
# chage -m 7 -M 365 chavez
# chuser maxage=52 minage=1 chavez
# usermod -x password_min_change_time=7 \
# chage -l harvey Minimum: 0 Maximum: 99999 Warning: 0 Inactive: -1 Last Change:
# Content controls
# Minimum password length
# unshadow /etc/passwd /etc/shadow > /secure/pwdtest
# chmod go= /secure/pwdtest
# john -rules -wordfile:/usr/dict/many_words /secure/pwdtest
# kill -HUP pid
# john -status
# john -status:urgent
# /etc/init.d/ldap start
# Domain entry
# Manager entry
# ldapadd -x -D "cn=Manager,dc=ahania,dc=com" -W -f /tmp/entry0 Enter LDAP Password: Not echoed
# ldapsearch -x -b 'dc=ahania,dc=com' -s base '(objectclass=*)' version: 2
# ahania,dc=com
# ldapsearch -x -b 'dc=ahania,dc=com' -S cn \ '(&(objectclass=person)(cn=Mike*))' \ telephoneNumber description
# ldapsearch -H ldap://bella.ahania.com -x -b 'dc=ahania,dc=com' \ '(cn=Mike*)' telephoneNumber description
# ldapsearch -x '(cn=Mike*)' telephoneNumber description
# migrate_passwd.pl /etc/passwd passwd.ldif
# getent passwd
# List of allowed hosts
# Limit host access to the specified users
# cd /usr/ssl/cert
# openssl req -newkey rsa:1024 -x509 -days 365 \
# openssl rsa -in slapd_key.pem -out slapd_key.pem # chown slapd-user.sldap-group sl*.pem
# chmod 600 sl*.pem
# ngrep 'bbb' port 636 or port 389
# ldapsearch -H ldaps://10.0.49.212:636 -w bbb -x \ -D 'uid=a2,ou=People,dc=ahania,dc=com' 'uid=a*'
# cd /bin
# mv login login.save
# ln -s opielogin login
# opiepasswd -c chavez
$ opiekey 123 ab4567
$ opiekey -n 3 123 ab4567
# passwd -F /etc/d_passwd /bin/sh
# chuser auth1=LOCALAUTH chavez
# chuser auth1=SYSTEM,LOCALAUTH chavez
# cp /bin/sh /tmp/.junk # chmod 4755 /tmp/.junk
$ echo $PATH /usr/ucb:/bin:/usr/bin:/usr/bin/X11:/usr/local/bin:$HOME/bin:.
# chmod u+s files setuid access # chmod g+s files setgid access
$ ls -lo /bin/passwd
$ aclput -i metal.acl gold 356 | Chapter 7: Security
$ aclget silver | aclput emerald
$ ls *.dat *.old | xargs -i /bin/sh -c "aclget silver | aclput {}"
$ chacl "(%.bio,r--) (hill.%,r-x)" silver $ chacl "%.bio = r, hill.% = rx" silver
$ chacl "%.chem -w+r, harvey.% +r" silver
$ chacl -r "@.% = 7, %.@ = rx, %.bio = r, %.% = " *.dat
$ chacl -f silver *.dat
$ chacl -r "`cat acl.metal`" *.dat
# setfacl -m user:harvey:r-x,group:geo:r-- gold Tru64
# setacl -u user:harvey:r-x,group:geo:r-- gold
# getfacl gold > acl; setfacl -f acl silver Linux
# getfacl gold > acl; setfacl -S acl silver Tru64
# getacl gold > acl; setacl -b -U acl silver
# setfacl -m d:o:r-x /metals Linux
# setfacl -d -m o:r-x /metals Tru64
# setacl -d -u o:r-x /metals
# getfacl -R --skip-base / > backup.acl # setfacl --restore=backup.acl
$ crypt key1 < clear-file | crypt key2 | crypt key3 > encr-file $ rm clear-file
$ pgp -kg $ gpg --gen-key
$ pgp -ka key-file $ gpg --import key-file
$ pgp -kxa key-file $ gpg -a --export -o key-file username
$ pgp -e -s file chavez@ahania.com $ gpg -e -s -r chavez@ahania.com file Simply encrypting a file for privacy purposes is much simpler; you just use the -c
$ pgp -c file $ gpg -c file
$ pgp encrypted-file $ gpg -d encrypted-file
# chuser roles=ManageAllUsers aefrisch
$ profiles Operator
$ auths | sed 's/,/ /g' | fold -s -w 30 | sort solaris.admin.printer.delete solaris.admin.printer.modify solaris.admin.printer.read solaris.admin.usermgr.pswd solaris.admin.usermgr.read
#STYLE = -DPROCESS_OPTIONS # Enable language extensions.
# nmap kali
# grep '^[^:]*::' /etc/passwd root::NqI27UZyZoq3.:0:0:SuperUser:/:/bin/csh demo::7:17:Demo User:/home/demo:/bin/sh ::0:0:::
$ su ""
# No password prompt!
# grep ':00*:' /etc/passwd root:NqI27UZyZoq3.:0:0:SuperUser:/:/bin/csh harvey:xyNjgMPtdlx*Q:145:0:Thomas G. Harvey:/home/harvey:/bin/ksh badguy:mksU/.m7hwkOa:0:203:Bad Guy:/home/bg:/bin/sh larooti:lso9/.7sJUhhs:000:203:George Larooti:/home/harvey:/bin/csh
# grep ':0:' /etc/passwd | grep -v root This won't catch everything.
# diff /etc/passwd /usr/local/bin/old/opg
$ cd /etc; ls -l *passwd* *shadow*
# Make sure output file is empty. /usr/bin/cp /dev/null perm.ck
# find / \( -perm -2000 -o -perm -4000 \) -type f -print
# find / -type f \( -perm -2000 -o -perm -4000 \) \
$ ls -lsid /etc/rc*
$ cksum /bin/* 09962 4 /bin/[ 05519 69 /bin/adb ...
# Pathname
$ ps axww | egrep 'PID|harvey'
$ ps aux | egrep 'PID|smith'
$ ps -auxc | egrep 'PID|smith'
$ ps -ef
# egrep '^[^*].*:$|gin_coun' /etc/security/user | \ awk '{if (NF>1 && $3>3) {print s,$0}} ; NF==1 {s=$0}' chavez: unsuccessful_login_count = 27
$ last
$ last chavez
$ last dalton console
$ last reboot
$ lastcomm
$ lastcomm chavez calculus
$ acctcom
$ acctcom -u root -n vi COMMAND
$TTL time
$ORIGIN domain
$GENERATE range record-template $INCLUDE path
$TTL 24h Lifetime for cached mappings. @ SOA IN ns.ahania.com. chavez.dalton.ahania.com. (
$ORIGIN 88.168.192.in-addr.arpa. 1 IN CNAME 1.sub0
$ORIGIN 88.168.192.in-addr.arpa.
$GENERATE 1-30  $ IN CNAME $.0-31
# Define a list of our name servers.
# Define another list.
# dnskeygen -H 128 -h -n apricot-mango.ahania.com. # dnssec-keygen -a HMAC-MD5 -b 128 -n HOST \
# cat Kapricot-mango.ahania.com.+157+52961.private Private-key-format: v1.2
# mknod /named-root/dev/null c 1 3 # mknod /named-root/dev/random c 1 8
# /etc/init.d/named restart
# kill `cat /var/run/named.pid`
# rndc reload Reload configuration and zone file.
# ndc reload
# rndc -s apricot stop Terminate a remote name server process.
$ nslookup > mango Server: Address:
$ nslookup
$ dig @bonita.ahania.com version.bind txt chaos ...
# enable RIP
# Dynamic DNS update commands
# dadmin -h kumquat -q 192.168.44.23
# touch /var/lib/dhcp/dhcpd.leases
# dhcrelay 10.10.30.1
# bprelay 10.10.30.1
# stopsrc -g tcpip # stopsrc -s named # startsrc -s inetd # startsrc -g nfs
# lssrc -g nfs Subsystem
# netstat | grep -v localhost
# netstat -i
# netstat -i 5 | awk 'NR!=3 {print $0}'
# netstat -s ...
# ping beulah 64 5
# traceroute www.fawc.org
# tcpdump -e -t host romeo
# tcpdump -X -s 0 src mozart and dst salieri
# tcpdump src \( mozart or salieri \) and tcp port 21 and not dst vienna
# cd /dev; MAKEDEV pfilt
# pfconfig +p +c network-interface
# snoop -o file -q # snoop -i file
# snoop -v [-p n]
# snmpget beulah somethingsecure sysLocation.0 system.sysLocation.0 = "Receptionist Printer"
# snmpget beulah somethingsecure laLoad.2 enterprises.ucdavis.laTable.laEntry.laLoad.2 = 1.22
# snmpwalk beulah somethingsecure host.hrSystem host.hrSystem.hrSystemUptime.0 = Timeticks: (31861126)
# snmpset beulah somethingelse sysContact.0 s "chavez@ahania.com" system.sysContact.0 = chavez@ahania.com
# snmptrap -v2c dalton anothername '' .1.3.6.1.6.3.1.1.5.3 \ ifIndex i 2 ifAdminStatus i 1 ifOperStatus i 1
# snmpinfo -c somethingsecure -h beulah -m get sysLocation.0 system.sysLocation.0 = "Receptionist Printer"
# snmp_request beulah somethingsecure get 1.3.6.1.2.1.1.6.0
# Net-SNMP-specific items: conditions for error flags
#keyw [args] limit(s)
#view   name
#group   grp name  model  sec. name
#view   name
#community
#trap  [version]  community  destination[:port]
#service[host] =label;; when;;;; notify;;;;;; check-command service[callisto]=TELNET;0;24x7;4;5;1;admins;960;24x7;0;0;0;;check_telnet service[callisto]=PROCS;0;24x7;4;5;1;admins;960;24x7;0;0;0;;snmp_nproc!commune!250!400 service[ingres]=HPJD;0;24x7;4;5;1;localhost;960;24x7;0;0;0;;check_hpjd
# pine configuration file
# set some options
# start editor immediately when composing mail message
# Make quoted messages in replies as short as possible
# -*-muttrc-*-
# PGP command formats for PGP 6.
# Programs that message text is piped into prior to display display-filters=_BEGINNING("-----BEGIN PGP")_ /usr/bin/pgp4pine -d -i TMPFILE # Programs that message text is piped into prior to sending sending-filters=/usr/bin/pgp4pine -e -i TMPFILE -r RECIPIENTS
# Post Office Protocol - Version 3
# Post Office Protocol - Version 3
# Internet Message Access Protocol
# Internet Message Access Protocol
$ echo | sendmail -bt -d0
$ telnet pauling 25
# /sbin/init.d/sendmail restart
# startsrc -s sendmail # refresh -s sendmail
# cd build-dir
# emacs test.mc
# ./Build test.cf
# cp /etc/mail/sendmail.cf /etc/mail/sendmail.cf.save # cp test.cf /etc/mail/sendmail.cf
# chmod 444 /etc/mail/sendmail.cf
# kill -HUP `head -1 /location/sendmail.pid`
# make test.cf
# m4 ../m4/cf.m4 test.mc > test.cf
$ grep Mlocal /etc/sendmail.cf
# cd /etc/mail; makemap hash senders < senders.txt
# mailq
# sendmail -oQ/var/spool/save_queue -q
# mailstats
# hoststat
# telnet kevin 25
# sendmail -v chavez@ahania.com < /dev/null
# sendmail -bv chavez
# sendmail -bt
# sendmail -v -d11.2 trucks < /dev/null
# /usr/sbin/sendmail -d27 lilith < /dev/null alias(lilith)
# postfix reload
# postmap hash:canonical
# postmap virtual
# telnet kevin.ahania.com 25
# fetchmail mailer.notaol.com
# Global parameters
# Append log to modified messages.
# Don't log to standard error also.
# Keep logging brief.
# Sanitize incoming shell scripts.
# Sanitize active HTML content.
# Sanitize forwarded messages.
# Use type-based scanning.
# We will define 3 groups.
# Rewrite risky constructs.
# Scan these file types for macros, viruses
#
$score += 99 while ($buff =~ s/\000Macro recorded/x$1/i);
# cat mail-file | /path/sanitizer.pl config-file
$ vacation -I
$ from
$ grepmail -i hilton ~/Mail/conf/acs_w01 | grep -i telephone Telephone: 619-231-4040
# ls -saC /chem
# mount /dev/dsk/c1d1s2 /chem # ls -saC /chem
# du -s /chem
# mkdir /users2
# mount /dev/dsk/c1t4d0s7 /users2
# mount -r /dev/dsk/c1t4d0s7 /mnt
$ fuser -u /dev/dsk/c1t1d0
# FreeBSD
# device
# Linux
# device
# HP-UX
# device
# Tru64
# device
# swap partition is defined in /etc/sysconfigtab
# mount /chem
# umount /dev/disk1d
# mount -a -t advfs
# mount -tnomsdos,nfs -a
# umount -h dalton
# fsck /dev/rdisk1e
# fsck -F ufs -o p /dev/dsk/c0t3d0s2
# reboot -n
# mknod name | major minor
$ cd /dev/dsk; ls -l c1d* Major, minor device numbers.
$ cd /dev/rdsk; ls -l c1d1*
# mknod /dev/dsk/c1d3s2 b 0 178 # mknod /dev/rdsk/c1d3s2 c 3 178
# cd /dev
# ./MAKEDEV sdb
# fdisk -i /dev/da1
# newfs /dev/da1a
# fsck /dev/da1a
# growfs /dev/da1a
# cd /dev; ./MAKEDEV sdp
# fdisk /dev/sda # cfdisk /dev/sda
# mkfs -t ext3 -j /dev/sda1
# tune2fs -l /dev/sdb1 Filesystem magic number: 0xEF53
# tune2fs -i 0 -c 25 /dev/sdb1
# tune2fs -j /dev/sdb2
# umount /dev/sdc1
# e2fsck -f /dev/sdc1
# resize2fs -p /dev/sdc1 200000
# mkreiserfs /dev/sdb3
# reiserfsck -x /dev/sdb3
###########
###########
# mount -o remount,resize=200000 /dev/sdc1
# format /dev/rdsk/c0t2d0s2
# newfs /dev/rdsk/c0t2d0s0
# fsck -y /dev/rdsk/c0t2d0s0
# dump 0 /dev/sda1
# restore -t
# mke2fs -b 4096 -j /dev/sda1
# lsvg rootvg
# lsvg -l rootvg
$ lsdev -C -c disk
# mkvg -y "chemvg" hdisk5 hdisk6
# extendvg chemvg hdisk4
# mklv -y "chome" chemvg 64
# crfs -v jfs2 -g chemvg -a size=50000 -a frag=1024 -m /organic2 -A yes # mount /organic2
# chfs -a size=+50000 /inorganic
# umount /chem2
# rmfs /chem2
# rmlvcopy chlv2 2 hdisk4
# shutdown -r now
# chpv -v r hdisk4
# reducevg chemvg2 hdisk4 # rmdev -l hdisk4 -d
# mklv -y cdata -S 64K chemvg 125 hdisk5 hdisk6 668 | Chapter 10: Filesystems and Disks
# mklv -c 2 -s s -w y biovg 500 hdisk2 hdisk3
# mklv -y chome -ac chemvg 64
# vgdisplay vg00
# pvcreate /dev/rdsk/c2t0d0
# mkdir /dev/vg01
# mknod /dev/vg01/group c 64 0x010000
# vgcreate /dev/vg01 /dev/dsk/c2t0d0
# vgcreate /dev/vg01 -g groupa /dev/dsk/c2t2d0 /dev/dsk/c2t4d0 # vgextend /dev/vg01 -g groupb /dev/dsk/c1t0d0 /dev/dsk/c1t1d0
# lvlnboot -r -s /dev/vg01/swaplv
# newfs /dev/vg01/rchemvg
# lvcreate -n cdata -L 200 -m 1 -s g /dev/vg01
# lvcreate -n tyger -L 400 -i 4 -I 64 /dev/vg01
# showfdmn root_domain Describe this domain.
# mountlist -v root_domain#root
# showfsets usr_domain
# addvol /dev/disk/dsk2c chem_domain # balance chem_domain
# mkfset chem_domain bronze
# mkfset chem_domain silver
# mount chem_domain#bronze /bronze # mount chem_domain#silver /silver
# renamefset chem_domain lead gold
# voldiskadd dsk3 dsk4 ...
# voldg -g dg1 adddisk dsk9 dsk10 dsk11
# volassist -g dg1 make chemvol 2g dsk3 dsk4
# volassist -g dg1 mirror chemvol init=active layout=nolog dsk5 dsk6 # volassist addlog chemvol dsk7
# volassist -g dg1 make stripevol 2g layout=stripe nstripe=2 dsk3 dsk4 This command creates a two-way striped volume named stripevol.* The following command will create a 3 GB RAID 5 volume:
# volassist -g dg1 make raidvol 3g layout=raid5 nstripe=5 disks
# voledit -g dg1 set spare=on dsk9 # volwatch -s lsmadmin@ahania.com
# metadb -a -f c0t0d0s7 Create initial state database replicas. # metadb -a -c 2 c1t3d0s2 Add replicas on this slice.
# umount /docs
# metainit d10 2 1 c0t0d0s6 1 c2t3d0s2 Add additional disk space.
# vi /etc/vfstab Change the filesystem's devices to /dev/md/[r]dsk/md10. # mount /docs
# growfs -M /dev/md/rdsk/d10 Increase the filesystem size to the volume size.
# metainit d2 1 2 c1t1d0s2 c2t2d0s2 -i 64k
# metainit d20 -f 1 1 c0t3d0s2 # umount /dev/dsk/c0t3d0s2
# metainit d21 1 1 c2t1d0s2
# metainit d22 -m d20
# vi /etc/vfstab
# mount /dev/md/dsk/d22 # metattach d22 d21
# metaparam -r geometric -w parallel d22
# metainit d30 -r disks -i 96k
# metareplace -e d30 c2t5d0s2
# metainit hsp001 c3t1d0s2 c3t2d0s2 # metaparam -h hsp001 d30
# metainit d7 -p c2t6d0s2 2g
# metainit d8 -p -e c2t5d0s2 3g
# fdisk /dev/sdb1
# pvcreate /dev/sdb1 /dev/sdc2
# vgcreate vg1 /dev/sdb1 /dev/sdc2
# lvcreate -L 800M -n chem_lv vg1
# mke2fs -j /dev/vg1/bio_lv
# umount /dev/vg1/bio_lv
# e2fsadm /dev/vg1/bio_lv -L+100M
# vinum create /etc/vinum.big.conf
# newfs -v /dev/vinum/big
# vinum create file
# vinum init mirror.p1 Wait for command to finish. # vinum start mirror.p1
# vinum list
# vinum create file Create the volume.
# vinum init safe.p0 Initialize the subdisks.
# vinum create file
# vinum init zebra.p0 zebra.p1 # vinum start zebra.p1
$ mediainit -v -i2 -f16 /dev/rdsk/c0t1d0 $ newfs -n /dev/rdsk/c0t1d0 ibm1440
$ doscp /dev/rdsk/c0d1s0:paper.txt paper.new
# fdformat /dev/fd0
# mkfs -t msdos /dev/fd0 # mount /dev/fd0 /mnt
# cp prop2.txt /mnt
# umount /mnt
# fdformat /dev/fd0
# newfs_msdos /dev/fd0 # mount /dev/fd0 /mnt # cp prop2.txt /mnt
# umount /mnt
$ volcheck
$ fdformat -d -b g04
$ volcheck
$ cp ~/proposals/prop2.txt /floppy/g96 $ cp /floppy/g96/drug888.dat ./data
$ eject
$ mdir
$ mren prop43_1.txt prop43_1.old
$ mcopy a:refcard.doc .
$ mcopy proposal.txt a:
$ mmd data2
$ mcopy gold* a:data2
$ mcopy "a:\data\*.dat" ./data Copying NA.DAT
$ mdel silver.dat
# mount -t msdos /dev/hdal /mnt # cd /mnt
# tar -c -f /tmp/dos.tar *
# unmount /mnt
# mount -t msdos /dev/hda1 /mnt # cd /mnt
# tar -x -f /tmp/dos.tar
# cd /; umount /mnt
# mkdev -c cdrom -r cdrom1 -s scsi -p scsi0 -w 5,0 cd0 available
# crfs -v cdrfs -p ro -d cd0 -m /cdrom -A no
$ lsfs -v cdrfs
# mount
# device
# kill -QUIT 34216
# mount /rings
# mount -t nfs -o rw,hard,bg,intr duncan:/ether /mnt
# exportfs -a
# kill -HUP `cat /var/run/mountd.pid`
# showmount -a brazil:/organic spain:/organic
# automount -tl 600 /homes /etc/auto.homes /- /etc/auto.direct
# kill `ps -ea | grep automoun | awk '{print $1}'`
$ smbstatus
# cat /etc/passwd | mksmbpasswd.sh > /etc/samba/private/smbpasswd
# mount -t smbfs -o username=user,password=xxx //vala/depot /win_stuff
# mount_smbfs -I vala //chavez@vala/depot /mnt Password: Not echoed.
# remote share mount point type options
$10 $120 $70 $140 $300 $340 $150 $150 $700 $700
$0.25 $8 $5 $12 $80 $100 $0.85 $1 $8 $40
$600 $550 $700 $1200 $1200 $2500 $3700 $900 $2600 $3900 $4000 $6000 $2300 $2700 N/A N/A
$8
$6 $12.50 $26
$6
$36
$45
$79
$85 $105 $70 $150 $65
$93 $2–3/GB $10/GB
$ lsattr -E -H -l rmt0
# chdev -l rmt0 -a block_size=1024 -a compress=no -a ret=no
$ tar -c /home
$ tar -cf /dev/rmt1 /home /home2 /chem/public
$ tar -cf /dev/rmt1 -C /home . -C /home2 . -C /chem public
$ tar cvfX /dev/rst0 Dont_Save /home -I Other_User_Files -I Misc
#
#Archive=Device
#
$ tar xfz funsoftware.tgz
$ find /home -print | cpio -o >/dev/rmt0 $ find /home -cpio /dev/rmt0
$ find /home -mtime -1 ! -name \*.o -print | cpio -o >/dev/rmt1 $ tar c1 `find /home -mtime -1 ! -name '*.o' ! -type d -print`
$ touch /backup/home_full
$ find /home -print | cpio -o > /dev/rmt0
$ touch /backup/home_incr_1
$ find /home -newer /backup/home_full -print | cpio -o > /dev/rmt0
$ pax -w -f /dev/rmt0 /home /chem
$ find /home /chem -mtime -1 -print | pax -w -f /dev/rmt0 $ pax -w -X -f /dev/rmt0 /
$ dump option-letters corresponding-arguments filesystem
$ dump 0sd s-argument d-argument filesystem
# fbackup -0u -f /dev/rmt/1m -i /chem
# fbackup -1u -i /chem -i /bio -e /bio/med
# fbackup -1u -f /dev/rmt/0m -f /dev/rmt/1m -i /chem
# fbackup -0u -g /backup/chemists.graph -I /backup/chemists.TOC
$ dd if=/dev/rmt0 of=/chem/data/c70o.dat \ ibs=1024 fskip=2 conv=swab
$ mt [-f tape-device] command
$ mt -f /dev/rmt1 rewind
$ mt -f /dev/nrsa0 comp on density 0x26
$ tar -xp /home/chavez/freeway/quake95.data $ cpio -im '*quake95.data' < /dev/rmt0
$ tar -xp /home/harvey
$ cpio -imd '/home/harvey*' < /dev/rmt0
$ pax -f /dev/rmt0 -v
$ pax -r '/h95/*.exe'
$ pax -r /home/chavez
$ pax -r -f my_archive -c '*.o' # pax -r -pe -f /dev/rmt0
$ pax -r -s ',^/home/chavez/,/home/harvey/,' \ -f /dev/rmt0 /home/chavez
$ restore option-letters corresponding-arguments [files-and-directories]
# restore -x -f /dev/rmt1 chavez/mystuff others/myprogram
$ restore -i -f /dev/rmt1 restore > help
$ mt -f /dev/rmt0 rewind If necessary $ mt -f /dev/nrmt0 fsf 2
# frecover -x -i /chem/fullerenes # frecover -r -f /dev/rmt/1m
$ dd if=/dev/rmt1 conv=swab | tar tvf -
$ today='date +%d%b%Y'
$ tar -cv /home > /backup/home_full_$today.TOC
$ tar -cv /home | tee /backup/home_full_$today.TOC
$ find /home -print | cpio -ov > /dev/rmt0 2> $toc
$ cpio -itv < /dev/rmt0 > $toc
$ find /home | cpio -o | tee /dev/rmt0 | cpio -t -i -v > $toc
#!/bin/csh
# bkup+toc - perform dump and verify tape/make TOC file
# $1 = filesystem
# $2 = dump level (default=0)
#
# frecover -r -Nv -f /dev/rmt/0m > $toc
# fbackup -0u -f backuphost:/dev/rmt/1m -i /chem The Amanda Facility
# fundamental parameters
# incremental level bump parameters
# number of tapes in use
#changerdev "/dev/whatever" #tpchanger "script-path" #runtapes 4
# When media is unavailable, save this % of holding space # for degraded-mode incremental backups.
# amount of space to use (+) or save (-); 0=use all (default) use -2 Gb Always leave this much space.
# use 24-hour clock without punctuation
# host
# Win2K system
# dump all alone
$ amlabel Daily DAILY05
$ amlabel Chem CHEM101 slot 5
# amadmin Daily tape
# amrecover Daily
$CWD '/home/chavez/data' is on disk '/home' mounted at '/home'. 200 Disk set to /home.
# mksysb -i /dev/rmt0
# restore -s 4 -x -q -f /dev/rmt0 ./bin/csh ./etc/mf
# savevg -i chemvg /dev/rmt1
# restvg -q -f /dev/rmt1
# restvg -q -s -f /dev/rmt1 hdisk4 hdisk5
# restore -f /dev/rmt1 -T -q
# restore -f /dev/rmt1 -x -q -d ./chem/src/h95
# dd if=/cdrom/floppies/fixit of=/dev/rfd0c bs=36b
# make_recovery -p -A -d /dev/rmt/1mn
# emacs /var/opt/ignite/recovery/arch.include # make_recovery -r -A -d /dev/rmt/1mn -C
# cd /
# mt -t /dev/rmt/1mn fsf 1
# tar xvf /dev/rmt/1m relative-pathname(s)
# dd if=/boot/file of=/dev/fd0
# dd if=/cdrom/disks/rescue of=/dev/fd0 bs=18k
# cd /dev
# ./MAKEDEV tty4
# mknod /dev/cul0p4 c 1 0x401 # mknod /dev/cua0p4 c 1 0x401
$ hostname
$ tty
$ rlogin duncan
# cap_mkdb /usr/share/misc/termcap
$ tset -m ":?vt100" TERM = (vt100)
$ TERM=`tset - -Q -m ":?vt100"` Bourne and Korn shells $ export TERM
$ eval `tset -sQ -m ":?vt100"`
$ stty option [value]
$ grpe^H^H
$ stty erase ^h
$ stty -a
# login prompt #next label
$ chdev -1 tty0 -a login=enable -a speed=19200 -a term=vt100 \ -a runmodes='hupcl,cread,table3' \
# chdev -l tty0 -a login=share ...
# kill -HUP 1
# to submit to the will of SAF. Why did we ever let them change inetd? # /usr/sbin/inetd -s
# sacadm -l
# sacadm -a -p mux0 -t ttymon -c /usr/lib/saf/ttymon \ -v `ttyadm -V` -y "MUX 0 ttymon" -n 9999
$ pmadm -l -p zsmon Use -L for a compact format.
# pmadm -a -p zsmon -s ttyb -i root -f u -v `ttyadm -V` \
# pmadm -a -p zsmon -s ttya -i root -f u -v `ttyadm -V` \
$ sttydefs -l 57600E -------------------------------------------- 57600E:57600 hupcl evenp:57600 evenp::57600E --------------------------------------------
# sttydefs -a 57600i -n 57600i -i "57600 erase ^h" \ -f "57600 sane crt erase ^?"
# sttydefs -r 1200
$ ps -t15
# cat file > /dev/ttyn
# lsuser -fa ttys rlogin root root:
# chuser ttys="/dev/lft,/dev/tty0" rlogin=false root
# chuser ttys="!/dev/tty0,!/dev/tty1" rlogin=false root
$ sendfax -s na-let \ -T 5 -t 2 \
# faxstat -s -l List faxes to send. HylaFAX scheduler on dalton.ahania.com: Running
# faxstat -r -l List faxes that have been received. HylaFAX scheduler on dalton.ahania.com: Running
$ faxrm 4
$ faxrm -a -h mahler 28 Password:
# su chavez -c "faxrm 4"
$ faxalter -p bulk -a 21:00 24
# faxinfo fax00027.tif /var/spool/fax/recvq/fax00027.tif:
# faxadduser -a olympus mercury This is the resulting entry:
# faxadduser -a olympus 'mercury@.*ahania\.com'
# dmesg | grep usb
# usbdevs
# mount -t msdos /dev/da0s4 /zip
# disklabel -w -r da0 zip250
# newfs /dev/da0c
# mount -t ufs /dev/da0c /somewhere
# lsusb | grep Bus
# usbmodules --device /proc/bus/usb/001/002 usbmouse
# mount -t vfat /dev/sda4 /zip
# mount -F pcfs /dev/dsk/c1t0d0s0:c /zip
# lprm -P ps 15
# lprm -P plot -
# lpc topq ps 12
# lpc lpc>
# lpc
# ps aux | grep lpd
# kill -9 208
# rm -f /dev/printer /var/spool/lpd.lock
# /usr/lib/lpd
# /etc/init.d/lpd restart
# laser printer
# cd /var/spool/lpd
# mkdir newps
# chown daemon.daemon newps
# chmod 755 newps
# ls -ld newps
# touch /var/adm/lp_acct/ps3
# chown daemon /var/adm/lp_acct/ps3 # chmod 755 /var/adm/lp_acct/ps3
# lpc up ps3
# /usr/sbin/lprsetup
# lpadmin -dPS2
$ lpstat -d
# lpstat -uchavez,jones
$ lpstat -pPS,LP2
$ lpstat -alaser
# cancel PS2 PS3 -uchavez -uharvey
# lpmove request-IDs new_dest # lpmove old_dest new_dest
# reject PS # accept PS
# disable -r"Changing toner cartridge; back by 11" PS # lpstat -pPS
# enable PS
$ lpstat -r scheduler is running
# lpshut
# /sbin/init.d/lp start
$ lpadmin -pPS4 -ePS3 -v/dev/ttd2
$ lpadmin -pWorkhorse -v/dev/ttd5 -mlaserjet4
# lpadmin -xSlow Managing device classes
# lpadmin -pPS2 -claser
# lpadmin -pPS1 -rlaser
# lpadmin -pPS7 -v/dev/ttd2 -mpostscript -claser
# lpadmin -pPS0 -g2
# lpalt -p7 PS0-21
# lpusers -d15
$ lp -dlaser -q25 long_file
$ lp -dlaser -q10 imp_file
# lpusers -d15
# lpusers -q10
# lpusers -q5 -uchavez,wang # lpusers -q0 -uharvey
# lpusers -ustein
# lp -iPS-313 -q2
# lp -iPS-314 -Himmediate
# lp -iPS-314 -Himmediate # lp -iPS-209 -Hhold
# lp -iPS-209 -Hresume Printer interface programs
#!/bin/sh
#!/bin/sh cat $6 2>&1
# Construct the banner page unless suppressed
# Print the files
# lpadmin -phamlas -v/dev/null -mrmodel -ob3 -ormhamlet -orplaser
# lpadmin -pColor -shamlet\!dj200
# lpadmin -pdj200 -shamlet
# lpadmin -php4k -v/dev/null -mnetstandard -o protocol=tcp \ -o dest=engprt
# accept PS3; enable PS3
# lpadmin -pPS1 -uallow:duncan\!all,hamlet\!all
# lpadmin -plaser -udeny:\!harvey,wang
# lpadmin -pPS1 -udeny:duncan\!idaho
# lpadmin -p exp2 -v /dev/term/b -c exper \
# lpalt -n1 -p7 -dPS6 laser-23 request id is PS6-78 (1 file)
# lpana
# qcan -P laser -X
# qmov -m laser -# 8
# qmov -m laser -P inkjet
# qmov -m laser -P inkjet -u chavez
# qhld -# 8
# qhld -P laser
# qhld -r -# 8
# qhld -r -P laser
# enq -# job-number -R new-priority
# enq -# 45 -R 22
# qadm -D laser:lp0
# chque -q laser -a "up = FALSE"
# chgsys -s qdaemon -O Turn off autorestarting
# enq -G Stop the queueing subsystem
# lssrc -s qdaemon
# lsvirprt -q queue -d device | more Creating and modifying print queues
# /usr/lib/lpd/pio/etc/piomkpq -A local -p generic -v osp \ -s parallel -r ppa0 -w 0 Parallel port information
# /usr/lib/lpd/pio/etc/piomkpq -A local -p generic \
# chque -q laser -a "discipline = sjn"
# chquedev -q laser -d lp0 -a "header = never"
# rmquedev -q tek -d lp2 # rmque -q tek
# lsdev -C -l lp2
# startsrc -s lpd
# kill -9 pid-of-lpd-process # /usr/lib/lpd
# lpshut
# /etc/init.d/lp start
# enq -G
$ lpr -Pmatisse@painters files
# lpc class laser check
# lpc class laser off
# lpc class laser A
# lpadmin -plj4 -D"Finance LaserJet" -L"Room 2143-A" \ -vsocket://192.168.9.23 -mlaserjet.ppd
# grep found no matches
# Look for extra system accounts
# opg is the previously saved copy
# lines only in /etc/passwd
# lines only in ./opg
# $1 = cutoff in blocks for new directories
# $2 = cutoff as block change for old directories # $3 = starting directory
# $4 = flags to du
# ignore blank lines
# Assumes IDE disks.
#!/bin/csh
# doc_sys - document system configuration--AIX version
$ ls -l /usr/local/admin/mail/last_lucia
#!/usr/bin/perl -w Executable location varies. # dr - copy all the files on a DOS diskette
# remove newline char
# loop until an argument doesn't begin with a "-"
#!/usr/bin/perl -w
# wgrep - windowed grep utility
$before = 3; $after = 3;
$show_stars = 0;
$show_nums = 0;
$sep = "**********\n";
$show_fname = 1;
$show_sep = 1;
# default window size
$after = 0;
$fhandle = $file eq "STDIN" ? STDIN : NEWFILE;
$lnum = "00000";
$nbef = 0; $naft = 0;
$matched = 0; $matched2 = 0;
# Jump to LOOP label
# loop over the lines in the file
# increment line number
# we're printing the match window
# if current line matches pattern
#   reset the after window count,
#   print preliminary stuff,
#   and print the line
# current line does not match
# end else not in after window
# end else curr. line not a match
# end if we're in a match
# after window is done
# no longer in a match
# reset the after window count
# end elseif before window is nonzero
# end else not in a match
# end while loop over lines in this file
# end foreach loop over list of files
# end of script proper
# we're still looking for a match
# we found one
# so set match flag
$print_flag = $_[0];
$i = 0; $j = 0;
# print before window
# end while
# end if print_flag
# clear line_buf array
# end of subroutine
# Extract the date from line 1.
# Window title.
$label=$main->Label(-text => "Message of the Day");
$label->pack;
# Window's text area.
$text=$main->Scrolled('Text', -relief => "sunken",
$text->insert("1.0", "$text_block");
$text->pack(-side=>"top", -expand=>1, -fill=>"both");
# Window's status area (bottom).
$status = $main->Label(-text=>"Last updated on $date",
$status->pack(-side=>"top", -fill=>"x");
# Add a Close button.
$button=$main->Button(-text => "Close Window",
$button->pack;
#!/usr/local/bin/expect Executable location may vary. # run_test_user - check security of user acct
# run the Unix clear commmand
# run the specified command
# put terminal in raw mode
# wait for a character
# get the root password (once!)
# assign password to variable
# turn off echoing
# prompt for password
# get and remember it
# turn echo back on
# ssh failed
# just go on to next host
# got a prompt
# install new files
# regular prompt: su failed
# exit su shell
# wait for prompt
# all done with this host
# accept anything
# terminate spawn command
# end for loop
# success, so set done to 1
# line in use; wait a bit
# poll failed so try again
# end expect
# end while
#include <stdio.h>
#include <sys/types.h> #include <unistd.h> main(argc,argv)
# cfrun smiley toby percy
# cfrun -v -- -- linux solaris
# end cell A
# listen for connections
# send input to cell to this address
# input map: multiplex input
# output map: set destination for inputs
# end cell sw
# chat_cli.stem
# create a portal for communicating with other hubs
# listen for messages from other hubs
# accept messages from any host
# define cell attributes
# target switch cell
# target hub
# label string
# called when the cell is created (boilerplate code)
# called whenever data is received by the cell
# switch cell name
# chat server hub name
$ nroff -man file | more
$ ps -ef
$ ps -el | head -1 ; ps -el | sort -nkr10 | head -5
$ pstree init-+-alarmd
$ ls /proc/1234
$ cat /proc/cpuinfo
# cd /proc/sys/kernel # cat panic
# echo "60" > panic
$ ulimit -a
$ ulimit -Ha time(seconds) file(blocks) data(kbytes) stack(kbytes) memory(kbytes) coredump(blocks)
$ ulimit -t unlimited $ ulimit -m 65536
# kill [-signal] pids
# killall -KILL find Processes that won’t die
$ /bin/nice - [[-] n] command
$ /bin/nice -6 bigjob
# nice -5 bigjob
# /bin/nice --5 bigjob
$ vmstat interval [count]
# renice 5 8201
$ renice -n 12 8201
# renice -n 10 3769
# ps -l -p 3769
# priocntl -s -p new-pri -i pid proc-id
# priocntl -s -p -5 -i pid 8733
# priocntl -s -p -5 -i ppid 8720
# schedtune -r 15 -d 15
# schedtune -t 1
$ dispadmin -g -c TS
# Time Sharing Dispatcher Configuration
# ts_quantum ts_tqexp ts_slpret ts_maxwait ts_lwait PRIORITY LEVEL
# dispadmin -c TS -s /etc/ts_sched.new
# priocntl -s -m -5 27163
# sysconfig -m proc
# sysconfig -q proc autonice_penalty
# sysconfig -Q proc autonice_penalty
# sysconfig -r proc autonice_penalty=6
$ pagesize 4096
$ free -m -o
$ vmstat 5 4
$ vmstat 5 procs
$ vmstat 5 procs
# schedtune
# vmtune
# of remote pgs sched-pageout = 0
# sysctl kern.maxprocperuid kern.maxprocperuid: 531
# sysctl kern.maxprocperuid=64 kern.maxprocperuid: 531 -> 64
# echo "5 33 80" > /proc/sys/vm/buffermem Solaris
# kstat -m unix -n system_pages | grep 'free ' cachefree 1966 Units are pages. lotsfree 1966
$ lsps -a
# swapinfo -tam
# mkfile 50m /chem/page_1
# swap -a /chem/page_1 0 102400
# swapon -l 5000 -r 10000 /chem
# lvcreate -l 125 -n swap2 -C y -r n /dev/vg01 # swapon /dev/vg01/swap2
# dd if=/dev/zero of=/swap1 bs=1024 count=8192 # mkswap /swap1 8192
# sync; sync
# swapon /swap1
# dd if=/dev/zero of=/swap1 bs=1024 count=8192 Create 8MB file.
# vnconfig -e vnc0 /swap1 swap Create pseudo disk /dev/vn0c
# mkps -a -n -s 50 chemvg
# chps -s 10 paging01
# swap -d /dev/dsk/c1d1s1 0 # swap -d /chem/page_1 0
# rmps paging01
$ iostat 6
$ sar -d interval [count]
$ sar -d 5 10
# lvcreate -L 800M -n bio_lv -r 8 -C y vg1 The valid range for -r is 2 to 120.
# chdev -l sys0 -a maxpout=33 -a minpout=16
$ du -k /home/chavez
$ du -k -s /home/chavez 34823 /home/chavez
$ du -k / | sort -rn
# quot file-system
# quot / /dev/sd0a (/): 6472 root 5234 bin
$ find / -name ".BAK.*" -o -name "*~" -print
# find / /bio /corp -atime +7 \( -name ".BAK.*" \
# cat /dev/null > /var/adm/sulog
# cd /var/adm
# tail -100 sulog >tmp # cat tmp > sulog
# cd /chem
# touch quotas
# chmod 600 quotas
# edquota username(s)
# edquota -p chavez wang harvey Setting the soft limit expiration period
# quotaon filesystem # quotaon -a
# quotaon /chem
# repquota -v /chem
$ quota -g chem
$ netstat -s -p tcp Output shortened. tcp:
$ nfsstat -rc Client rpc:
# cd /
# mkdir -p /usr/src/sys If not already present. # mount /cdrom
# cat /cdrom/src/ssys.[a-d]* | tar xzvf -
# shutdown -r now
# bcheckrc or lsmbstartup # sizer -n NEWDEVS
# cd /usr/src/sys/i386/conf # cp GENERIC NEWKERN
# chmod +w NEWKERN
# emacs NEWKERN
# cd /usr/sys/conf
# cp GENERIC NEWKERN
# chmod +w NEWKERN
# emacs NEWKERN [/tmp/NEWDEVS]
#options QUOTA Tru64
# config NEWKERN
# cd ../../compile/NEWKERN # make depend
# make
# mv /kernel /kernel.save # make install
# doconfig -c NEWKERN # cd ../NEWKERN
# make depend
# make vmunix
# mv /vmunix /vmunix.save # cp ./vmunix /
# sysctl -a
# sysctl kern.maxfiles=1066 kern.maxfiles: 1064 -> 1066
# boot0cfg -B /dev/ad0
# boot0cfg -B -m 0x4 /dev/ad0
# disklabel -B /dev/ad0s1
# sysconfig -s
# sysconfig -m | grep dynamic hwautoconfig: dynamic
# sysconfig -Q lsm Parameters for the Logical Storage Manager lsm:
# sysconfig -q lsm Enable_LSM_Stats lsm:
# sysconfig -r lsm Enable_LSM_Stats=1 Enable_LSM_Stats: reconfigured
# cd /stand
# mv vmunix vmunix.save
# cd build
# /usr/lbin/sysadm/system_prep -v -s system
# kmtune -s var=value -S /stand/build/system
# mk_kernel -s ./system -o ./vmunix_new
# kmupdate /stand/build/vmunix_new
# mv /stand/system /stand/system.prev
# mv /stand/build/system /stand/system
# sysdef
# cp /boot/vmlinuz /boot/vmlinuz.save
# cd /usr/src
# bzip2 -dc linux-2.4.x.tar.bz2 | tar xvf -
# for p in patch-list; do
# cd /usr/src/linux
# cp arch/i386/config.in{,.save}
# cp .config .config.save
# make mrproper
# make xconfig
# ls -ld /usr/include/{linux,asm}
# ls -ld include
# make dep
# make clean
# emacs Makefile
# make bzImage # make install
# make modules
# make modules_install
# mkinitrd /boot/initrd-suffix suffix
# lilo -C /etc/lilo.conf
# first boot selection
# kernel argument for ancient Sony CD-ROM
# another Linux boot selection
# a Windows 2000 selection
# lilo -b /dev/hda1 -C /etc/lilo.conf
# dd if=/dev/hda2 of=linux.ldr bs=512 count=1
# lilo -r /mnt The boot.message file
# grub-install '(hd0)'
# grub-install -root-directory /boot '(hd0)'
# mount -t msdos /dev/fd0 /floppy Mount floppy. # cp /boot/vmlinuz /floppy Copy kernel.
# umount /floppy
# syslinux /dev/fd0 Add loader program to floppy.
# modinfo -d 3c59x Description
# map generic Ethernet interface to the specific device type alias eth0 3c59x
# lsattr -EHl sys0
# chdev -l sys0 -a maxuproc=500
# cd /var/adm
# /usr/sbin/accton
# mv pacct pacct.sav
# cat /dev/null > pacct
# /usr/sbin/accton pacct
# sa -s pacct.sav > /dev/null # rm -f pacct.sav
# mv /var/adm/acct /var/adm/acct.sav # touch /var/adm/acct
# sa -s /var/adm/acct.sav >/dev/null # rm -f /var/adm/acct.sav
# sa 11238 4299 12648 6489 4
# sa -D 225 4299 9205
# sa -b -r
# sa -m
# sa -l 11238 4299 12648
# sa -f -v 100
# ac
# ac chavez wang fine total 1588.65
# ac -d
# ac -d -p chavez wang
# cd /var/adm/acct
# mkdir -m 755 fiscal nite sum # chown adm.adm fiscal nite sum
# chargefee chavez 10
# chfs -a accounting=true /chem
# /bin/su - adm -c /usr/sbin/acct/startup
# OF PROCS
# DISK SAMPLES
# projadd -c "Animation Project" -U chavez -G grarts,design -p 105 animate
# projadd cad
# projmod -U chavez,wong animate
# projmod -l cae cad
# projdel y2k
# projects chavez default chemdev animate
$ newtask -p animate
# acctadm -e extended,host,mstate -f /var/adm/exact/task task
# acctadm -e extended,host,mstate -f /var/adm/exact/pacct process
# acctadm
# upacct cmds.hamlet -p /var/adm/pact
# upacct cmds.0 cmds.hamlet cmds.dalton cmds.garden
# ucomm.sh chavez l510.exe -- tail -- top --
# pac -m -p0.12 -Plistings
#!/bin/sh
$ cat show_shift #!/bin/sh
$ show_shift a1 a2 a3 a4 a1 a2 a3
$0 refers to the command or script name, as in this example:
$?
$$
$!
$ cat braces #!/bin/sh
${var-string} ${var+string} ${var=string} ${var?string}
$var string
$var string (set var=string) $var (display var:string only)
$ name=rachel
$ echo ${name-tatiana}
$ echo ${name2-tatiana}
$ echo ${name=tatiana}
$ echo ${n2=tatiana}; echo $n2 tatiana
$ echo ${name+tatiana}
$ echo name3=${name3+tatiana} name3=
$ name4=${name3?"no name given"} name3: no name given
$ echo name4=$name4
$ dir=${name-`pwd`}; echo $dir rachel
$ dir=${name3-`pwd`}; echo $dir /home/chavez
$ names="applepearplum" $ echo ${names:5} pearplum
$ echo ${names:5:4} pear
$ echo ${names:(-4):4} plum
$ names="applepearplum" $ echo ${names#apple} pearplum
$ echo ${names#a*p}
$ echo ${names#pear} applepearplum
$ echo ${names##a*p} Remove longest match. lum
$ echo ${names%%e*m} appl
# extract service name
$ names="applepearplum" $ echo ${names/p/X} aXplepearplum
$ echo ${names//p/X} aXXleXearXlum
$ echo ${names/%plum/kumquat} applepearkumquat
# NETDEV_n
# value of NETDEV_n
# IFCONFIG_n
# value of IFCONFIG_n
$ cat choose.bash #!/bin/bash
$ choose.bash 1) aix
$ who -r
$ echo $6
$ a=c; b=m; c=d; cmd=date $ echo $a$b$c
$ eval $`echo $a$b$c`
$ eval `tset -sQ -m ":?vt100"`
# pages=21; host=hamlet; user=chavez
# printf '%7.2f\t%s:%s\n' "$pages" "$host" "$user"
# n=27; n1=-23
# printf '*%7.1f* *%-7.1f* \n' $n $n
# printf '%-5.1f\n%-5.1f\n%-+5.1f\n%- 5.1f\n' $n $n1 $n $n 27.0
$ cat count_to_5 #!/bin/sh
$ count_to_5 1
# add one to i
$ echo $(( 5+8/2-1 )) 8
$ a='1+2'; echo $a 1+2
$ let a='1+2'; echo $a
$ declare -i a; a='1+2'; echo $a 3
$ a=(aaa bbb [5]=eee ddd) $ echo ${a[4]} ${a[5]} ddd eee
$ echo ${a[3]:-undefined} undefined
$ a=(x y z); echo ${a[4]:-undefined} undefined
$ echo ${#a[@]} 3
# sserv: function to start a server
# args: $1=daemon pathname; $2!="" means use startsrc #
$1 fi }
MX records, 429
$TTL directive, 429
$GENERATE directive, DNS, 435 generic top-level domains, 415 GENERICS_DOMAIN_FILE macro, 556 generics_entire_domain sendmail
Index | 1135
$TTL directive, DNS, 429 ttmkfdir package, 884








# Network Commands

bsd4# ps -aux
bsd4# netstat -a
bsd1# netstat -rn Routing tables
bsd1# netstat -i
bsd2# lsof COMMAND PID swapper 0 swapper 0 init 1 init 1 init 1 ...
bsd2# lsof -i
bsd2# lsof -i@sloan.lander.edu
bsd1# ifconfig -a
sol1# arp -a
bsd1# portscan 205.153.63.239 1 10000 -vv
lnx1# telnet 205.153.63.239 25
bsd1# ipfw list
sol1# ndd /dev/arp ? ?
sol1# ndd /dev/arp arp_cache_report
bsd1# sysctl net.inet.ip.forwarding net.inet.ip.forwarding: 0
sol1# ping 205.153.63.30 205.153.63.30 is alive sol1#
sol1# ping www.microsoft.com no answer from microsoft.com sol1#
bsd1# ping www.bay.com
bsd1# ping 172.16.4.1
bsd1# ping 172.16.3.10
bsd1# ping 172.16.3.10
bsd1# ping -f 172.16.2.12
bsd1# ping -f 172.16.2.20
bsd1# fping 172.16.2.10 172.16.2.11 172.16.2.12 172.16.2.13 172.16.2.14 172.16.2.13 is alive
bsd1# fping -u 172.16.2.10 172.16.2.11 172.16.2.12 172.16.2.13 172.16.2.14 172.16.2.14
bsd1# echoping -v lnx1
bsd1# echoping -v -h /ms.htm www.microsoft.com:80
bsd2# arping -v -c3 00:10:7b:66:f7:62
bsd1# traceroute 205.160.97.122
bsd1# pathchar 165.166.0.2
bsd1# bing -e10 -c1 205.153.60.2 165.166.36.17
bsd2# ttcp -r -s
bsd1# ttcp -t -s 205.153.63.239
bsd1# netserver
bsd1# netperf
bsd1# netserver
bsd2# netperf -H 205.153.60.247
bsd2# netperf -H 205.153.60.247 -tTCP_RR
bsd2# iperf -s -p3000
bsd2# treno 205.153.63.30
bsd2# netstat -i
bsd2# netstat -Iep0
lnx1# netstat -i
bsd2# netstat -Iep0
bsd1# spray -c1000 205.153.63.239
bsd2# netstat -Iep0 Name Mtu Network ep0 1500 <Link>
bsd2# netstat -s -s ip:
bsd2# netstat -p tcp -s -s tcp:
bsd1# tcpdump –l | tee outfile
bsd1# tcpdump -w outfile &
bsd1# tcpdump: listening on xl0
bsd1# kill 70260
bsd1# tcpdump –w rawfile
bsd1# tcpdump –r rawfile > textfile
bsd1# tcpdump –c100
bsd1# tcpdump –i xl1
bsd1# tcpdump –s200
bsd1# tcpdump -c1 host 192.31.7.130
bsd1# tcpdump -c1 -f host 192.31.7.130
bsd1# hexdump -C tracefile
bsd1# tcpdump host 205.153.63.30
bsd1# tcpdump ether host 0:10:5a:e3:37:c
bsd1# tcpdump dst 205.153.63.30
bsd1# tcpdump net 205.153.60
bsd1# tcpdump net 205.153.60.0 mask 255.255.255.0
bsd1# tcpdump net 205.153.60.0
bsd1# tcpdump ip
bsd1# tcpdump tcp
bsd1# tcpdump ip proto ospf bsd1# tcpdump ip proto 89
bsd#1 tcpdump ip proto 6
bds#1 tcpdump port 53
bsd#1 tcpdump udp port domain
bsd1# tcpdump greater 200
bsd1# tcpdump "ip[9] = 6"
bsd1# tcpdump tcp
bsd1# tcpdump 'ether[0] & 1 != 0'
bsd1# tcpdump "tcp[13] & 0x03 != 0"
bsd1# tcpdump host 205.153.63.30
bsd1# tcpdump host 205.153.63.30 and ip
bsd1# tcpdump host 205.153.63.30 and not ip
bsd1# tcpdump host lnx1 and udp or arp bsd1# tcpdump "host lnx1 and (udp or arp)"
lnx1# tcpdump –n "not (tcp port telnet and host lnx1 and host bsd1)"
bsd1# sanitize-tcp tracefile
bsd1# tcpdpriv -P99 -C4 -M20 –r tracefile –w outfile
bsd1# tcpdpriv -P99 -C4 -M20 -r tracefile -w- | tcpdump -r-
bsd1# tcp-reduce tracefile > outfile
bsd1# tcp-reduce out-file | tcp-summary
bsd1# tcpshow < trace-file
bsd1# tcpslice 934224220.0000 in-file > out-file
bsd1# tcptrace -l telnet.trace
bsd2# ifconfig ep0
bsd1# nmap sol1
bsd1# nmap lnx1/24
bsd1# nmap 172.16.2.230-235,240
bsd1# nmap -sP lnx1/24
bsd1# nmap -sP -PI lnx1/24
bsd1# grep arpwatch /var/log/messages > /tmp/arp.list
bsd1# telnet 172.16.2.233 25
bsd1# telnet 172.16.2.230 80 Trying 172.16.2.230... Connected to 172.16.2.230. Escape character is '^]'. GET HTTP/1.0
bsd1# queso 172.16.2.230 172.16.2.230:80 * Windoze 95/98/NT
bsd1# queso 172.16.2.1
bsd1# queso -p23 172.16.2.1
bsd1# queso -p23 172.16.2.232/29 172.16.2.233:23 * Solaris 2.x
bsd1# queso -p23 205.153.60.1
bsd1# nmap -O 172.16.2.230
bsd1# nmap -p80 -O 172.16.2.230
bsd2# tclsh8.2 %
bsd4# scotty
bsd4# cat /etc/hosts 127.0.0.1 205.153.63.239 205.153.63.239 205.153.60.231 205.153.60.233 205.153.60.234
bsd4# snmpget 172.16.1.5 public .1.3.6.1.2.1.1.1.0
bsd4# snmpget 172.16.1.5 public iso.org.dod.internet.mgmt.mib-2.system.sysDescr.0 system.sysDescr.0 = "APC Embedded PowerNet SNMP Agent (SW v2.2, HW vB2, Mod: AP9 605, Mfg 08/10/96, SN: WA9632270847, Agent Loader v1.0)"
bsd4# snmpget 172.16.1.5 public system.sysDescr.0
bsd4# snmpget 172.16.1.5 public .1.3.6.internet.2.1.system.1.0
bsd4# snmpget 172.16.1.5 public upsBasicIdentModel.0 enterprises.apc.products.hardware.ups.upsIdent.upsBasicIdent.upsBasicIdentModel. 0 = "APC Smart-UPS 700 "
bsd4# snmpget 172.16.1.5 public .1.3.6.1.4.1.318.1.1.1.1.1.1.0 enterprises.apc.products.hardware.ups.upsIdent.upsBasicIdent.upsBasicIdentModel. 0 = "APC Smart-UPS 700 "
bsd4# snmpget -On 172.16.1.5 public .1.3.6.1.4.1.318.1.1.1.1.1.1.0
bsd4# snmpget 172.16.1.5 public ip.ipForwarding.0 ip.ipForwarding.0 = not-forwarding(2)
bsd4# snmpget -Oe 172.16.1.5 public ip.ipForwarding.0 ip.ipForwarding.0 = 2
bsd4# snmpget -Os 172.16.1.5 public enterprises.318.1.1.1.1.1.1.0 upsBasicIdentModel.0 = "APC Smart-UPS 700 "
sd4# snmpget -OS 172.16.1.5 public enterprises.318.1.1.1.1.1.1.0 PowerNet-MIB::upsBasicIdentModel.0 = "APC Smart-UPS 700 "
bsd4# snmpget -Of 172.16.1.5 public enterprises.318.1.1.1.1.1.1.0 .iso.org.dod.internet.private.enterprises.apc.products.hardware.ups.upsIdent. upsBasicIdent.upsBasicIdentModel.0 = "APC Smart-UPS 700 "
bsd4# snmpget -Os 172.16.1.5 public sysDescr.0
bsd4# snmpgetnext -Os 172.16.1.5 public sysDescr.0 sysObjectID.0 = OID: smartUPS700
bsd4# snmpgetnext -Os 172.16.1.5 public sysObjectID.0 sysUpTime.0 = Timeticks: (77951667) 9 days, 0:31:56.67
bsd4# snmpgetnext -Os 172.16.1.5 public sysUpTime.0 sysContact.0 = Sloan
bsd4# snmpwalk 172.16.1.5 public system
bsd4# snmpwalk 172.16.2.1 public enterprises | wc 3320 10962 121987
bsd4# snmptable -Cb -Cw 80 172.16.2.1 SNMP table: ip.ipRouteTable
bsd4# snmpset 172.16.1.5 private sysContact.0 s "el Zorro" system.sysContact.0 = el Zorro
bsd4# snmptranslate system.sysContact.0 .1.3.6.1.2.1.1.4.0
bsd4# snmptranslate -On .1.3.6.1.2.1.1.4.0 system.sysContact.0
bsd4# snmptranslate -Ofn system.sysContact.0 .iso.org.dod.internet.mgmt.mib-2.system.sysContact.0
bsd4# snmptranslate -IR sysContact.0 .1.3.6.1.2.1.1.4.0
bsd4# snmptranslate -On -Ib 'sys.*ime' system.sysUpTime
bsd4# snmptranslate -Td system.sysContact .1.3.6.1.2.1.1.4
bsd4# snmptranslate -Tp system +--system(1)
bsd4# snmpnetstat 172.16.2.234 public -an
bsd4# snmpnetstat 172.16.2.1 public -rn Routing tables
bsd4# snmpnetstat 172.16.2.1 public -i
bsd4# snmpnetstat 172.16.2.1 public -s -P ip ip:
bsd4# snmpstatus 172.16.2.1 public
bsd4# snmpwalk 172.16.2.234 public dskTable enterprises.ucdavis.dskTable.dskEntry.dskIndex.1 = 1 enterprises.ucdavis.dskTable.dskEntry.dskPath.1 = / enterprises.ucdavis.dskTable.dskEntry.dskDevice.1 = /dev/sda1 enterprises.ucdavis.dskTable.dskEntry.dskMinimum.1 = 10000 enterprises.ucdavis.dskTable.dskEntry.dskMinPercent.1 = -1 enterprises.ucdavis.dskTable.dskEntry.dskTotal.1 = 202182 enterprises.ucdavis.dskTable.dskEntry.dskAvail.1 = 133245 enterprises.ucdavis.dskTable.dskEntry.dskUsed.1 = 58497 enterprises.ucdavis.dskTable.dskEntry.dskPercent.1 = 31 enterprises.ucdavis.dskTable.dskEntry.dskErrorFlag.1 = 0 enterprises.ucdavis.dskTable.dskEntry.dskErrorMsg.1 =
lnx1# df -k /
bsd4# snmpwalk 172.16.2.236 private extTable enterprises.ucdavis.extTable.extEntry.extIndex.1 = 1 enterprises.ucdavis.extTable.extEntry.extNames.1 = datetest enterprises.ucdavis.extTable.extEntry.extCommand.1 = /bin/date -j -u enterprises.ucdavis.extTable.extEntry.extResult.1 = 0 enterprises.ucdavis.extTable.extEntry.extOutput.1 = Mon Jun 26 14:10:50 GMT 2000 enterprises.ucdavis.extTable.extEntry.extErrFix.1 = 0 enterprises.ucdavis.extTable.extEntry.extErrFixCmd.1 =
bsd4# snmpset 172.16.2.236 private versionUpdateConfig.0 i 1 enterprises.ucdavis.version.versionUpdateConfig.0 = 1
bsd4# snmpd -f -L -d
bsd4# snmpd -f -L -V
lnx1# scotty
lnx1# intop -i eth0
bsd2# cfgmaker public@172.16.2.1 > mrtg.cfg
bsd2# cat test.cfg
bsd2# indexmaker mrtg.cfg > /usr/local/www/data/mrtg/index.html
bsd2# mrtg test.cfg
bsd2# snmptranslate -Tp rmon.statistics +--statistics(1)
bsd2# snmpwalk 172.16.1.9 public rmon.1.1.1.5 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.1 = 36214 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.2 = 0 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.3 = 3994 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.4 = 242 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.5 = 284 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.6 = 292 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.7 = 314548 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.8 = 48074 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.9 = 36861 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.10 = 631831 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.11 = 104 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.12 = 457157 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.25 = 0 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.26 = 0 rmon.statistics.etherStatsTable.etherStatsEntry.etherStatsPkts.27 = 0
lnx1# hping 205.153.63.30
lnx1# hping2 -a 205.153.63.30 172.16.2.236
bsd2# hping -2 -p 53 -E data.dns -d 31 205.153.63.30
bsd2# hping -c 1 -j 172.16.2.230
bsd2# nemesis-tcp -v -D 205.153.63.30 -S 205.153.60.236
bsd2# nemesis-tcp -S 172.16.2.236 -D 205.153.63.30 -fS -fA -y 22
bsd2# nemesis-icmp -v -S 172.16.2.236 -D 205.153.63.30 -i 8
bsd2# nemesis-dns -v -S 172.16.2.236 -D 205.153.63.30 -q 1 -P data.dns
bsd2# ipsend -v -i ep0 -g 172.16.2.1 -d 205.153.63.30
bsd1# nc -l -p 2000
bsd2# nc 172.16.2.231 2000
bsd1# socket -s 2000
bsd2# socket 172.16.2.231 2000
bsd1# nc -l -p 2000 > tmp
bsd2# cat README | nc 172.16.2.231 2000 ^C punt!
bsd2# spray sol1
sol1# spray bsd2
bsd2# mgen -i ep0 demo
bsd2# telnet mail.lander.edu 25 Trying 205.153.62.5... Connected to mail.lander.edu.
bsd2# telnet mail.lander.edu 110 Trying 205.153.62.5...
bsd2# telnet localhost http
lnx1# telnet bsd2 ftp
sol1# nslookup 205.153.60.20 Server: lab.lander.edu
bsd2# nslookup www.lander.edu
sol1# nslookup
bsd2# dig @lander.edu www.lander.edu
bsd2# dig -x 205.153.63.30
bsd2# doc lander.edu.
bsd2# dnswalk lander.edu.
bsd2# ripquery 172.16.2.1
bsd2# rtquery 172.16.2.1
bsd2# rpcinfo -p bsd1 program vers proto port
bsd2# nfsstat -c Client Info:
bsd1# xwd -out xwdfile
bsd1# arp -a | tee outfile
bsd1# script scriptfile
bsd1# #Well, the foo program didn't work. \ >Let's try the bar program.
bsd1# exit
bsd1# script scriptfile ifconfig -a
bsd1# kill -HUP 127
bsd1# logger -p local7.debug "testing"
bsd2# ntpdate 205.153.60.20
bsd2# ntpq -p 172.16.2.1
bsd2# ntptrace 172.16.2.1
bsd2# siggen siggen


C:\>arp -a
C:\>ping www.cabletron.com
C:\>
C:\>ping www.microsoft.com
C:\>ping
C:\>tracert 205.153.61.178
C:\>iperf -c205.153.60.236 -p3000
C:\>netstat -e Interface Statistics

