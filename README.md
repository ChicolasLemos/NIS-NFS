# NIS-NFS

> AWS - First create 3 Machines in the same VPC, 1 server and 2 clients

Server (NFS Configuration)
```
nano /etc/hosts
 - add your server ip and your server name/domain ( xxx.xxx.xxx.xxx server.domain server domain )
apt update && apt upgrade -y && apt install nfs-kernel-server -y
nano /etc/exports
 - add /var/homes *(rw,sync,no_subtree_check,no_root_squash)
mkdir /var/homes
exportfs -a
systemctl enable --now nfs-kernel-server
adduser youruser --home /var/homes/youruser
echo xfce4-session > /var/homes/youruser/.xsession
chown youruser:youruser /var/homes/youruser/.xsession
```
Client (NFS Configuration)
```
nano /etc/hosts
 - add your server ip and your server name/domain ( xxx.xxx.xxx.xxx server.domain server domain )
apt update && apt -y upgrade && apt install nfs-common
mkdir /var/homes
mount -t nfs xxx.xxx.xxx.xxx:/var/homes /var/homes/ (put the server private ip)
cat /etc/mtab (copy the line with the ip)
nano /etc/fstab (paste the line and add "nofail,"
```
Server (NIS Configuration)
```
apt -y install nis
 put your domain
nano /etc/default/nis
 - NISSERVER=master
nano /etc/ypserv-securenets
# This line gives access to everybody. PLEASE ADJUST!
  255.255.255.0         172.31.0.0
# add to the end: IP range you allow to access
# 0.0.0.0               0.0.0.0
  255.255.255.0         xxx.xxx.xxx.xxx (server private ip)
nano /var/yp/Makefile
 - MERGE_PASSWD=true
 - MERGE_GROUP=true
/usr/lib/yp/ypinit -m
 - do Control+D and then type YES
systemctl restart nis
(if you add users in server use the nexts command)
cd /var/yp
make
```
Client (NIS Configuration)
```
apt -y install nis
 -put your domain
nano /etc/yp.conf
 -add the end put your domain (server name) server (server name and domain)
nano /etc/nsswitch.conf
 - add nis at the end of line in *passwd* *group* *shadow* and *hosts*
nano /etc/pam.d/common-session
 - add at the end (session optional        pam_mkhomedir.so skel=/etc/skel umask=077)
systemctl restart nis
```
