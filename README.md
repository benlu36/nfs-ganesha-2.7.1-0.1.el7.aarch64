README:

This document is about to build out arm64/aarch64 nfs-ganesha rpm pkgs for NeoKylin Linux or Centos / RedHat Linux.  The purpose to build this is because we cannot find them in Ceph official repositories at http://download.ceph.com/nfs-ganesha/rpm-V2.7-stable/mimic/aarch64/

## build nfs-ganesha 

Build from what OS:

[root@localhost ~]# cat /etc/*release;
NeoKylin Linux Advanced Server release V7Update5 (Vanadium)
NAME="NeoKylin Linux Advanced Server"
VERSION="V7Update5 (Vanadium)"
ID="neokylin"
ID_LIKE="fedora"
VARIANT="Server"
VARIANT_ID="server"
VERSION_ID="V7Update5"
PRETTY_NAME="NeoKylin Linux Advanced Server V7Update5 (Vanadium)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:neokylin:enterprise_linux:V7Update5:GA:server"
HOME_URL="https://www.cs2c.com.cn/"
BUG_REPORT_URL="https://bugzilla.cs2c.com.cn/"

NEOKYLIN_BUGZILLA_PRODUCT="NeoKylin Linux Advanced Server 7"
NEOKYLIN_BUGZILLA_PRODUCT_VERSION=V7Update5
NEOKYLIN_SUPPORT_PRODUCT="NeoKylin Linux Advanced Server"
NEOKYLIN_SUPPORT_PRODUCT_VERSION="V7Update5"
Red Hat Enterprise Linux Server release 7.5 (Maipo)
NeoKylin Linux Advanced Server release V7Update5 (Vanadium)
[root@localhost ~]# 

## Get source code to build:

[root@localhost tmp]# wget http://74.120.223.226/nfs-ganesha/rpm-V2.7-stable/mimic/SRPMS/nfs-ganesha-2.7.1-0.1.el7.src.rpm;


## prepare the build env:

[root@localhost tmp]# yum install gcc git cmake autoconf libtool bison flex -y;

[root@localhost tmp]# yum install -y libgssglue-devel openssl-devel nfs-utils-lib-devel doxygen redhat-lsb gcc-c++ rpm-build;


[root@localhost tmp]# mkdir -p /root/rpmbuild/BUILD /root/rpmbuild/SPECS /root/rpmbuild/SOURCES /root/rpmbuild/BUILDROOT /root/rpmbuild/RPMS /root/rpmbuild/SRPMS;

[root@localhost tmp]# ls -l ~/rpmbuild/;
total 0
drwxr-xr-x. 2 root root 6 Jun  7 15:23 BUILD
drwxr-xr-x. 2 root root 6 Jun  7 15:30 BUILDROOT
drwxr-xr-x. 2 root root 6 Jun  7 15:30 RPMS
drwxr-xr-x. 2 root root 6 Jun  7 15:30 SOURCES
drwxr-xr-x. 2 root root 6 Jun  7 15:23 SPECS
drwxr-xr-x. 2 root root 6 Jun  7 15:30 SRPMS

  
## setup ceph repo and install all ceph pkgs as depends for nfs-ganesha aarch64.rpm pkgs build, this step may not needed in some centos like OS:

[root@localhost tmp]# vi install_ceph_mimic_on_centos.sh;

[root@localhost tmp]# cat install_ceph_mimic_on_centos.sh;
sudo yum update;
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm;
sudo yum install -y yum-utils;
###################################################################
sudo rm -f /etc/yum.repos.d/ceph.repo;
cat << EOF > /etc/yum.repos.d/ceph.repo
[ceph]
name=Ceph packages for \$basearch
baseurl=https://download.ceph.com/rpm-mimic/el7/\$basearch
enabled=1
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[ceph-noarch]
name=Ceph noarch packages
baseurl=https://download.ceph.com/rpm-mimic/el7/noarch
enabled=1
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[ceph-source]
name=Ceph source packages
baseurl=https://download.ceph.com/rpm-mimic/el7/SRPMS
enabled=0
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc
EOF
##########################################################
sudo rpm --import 'https://download.ceph.com/keys/release.asc';
sudo yum clean all;
sudo rm -rf /var/cache/yum;
sudo yum install -y ceph ceph-osd ceph-mds ceph-mgr ceph-radosgw ceph-mon ceph-common ceph-test;
which ceph;
ceph --version;

[root@localhost tmp]# ./install_ceph_mimic_on_centos.sh;
[root@localhost tmp]# yum install -y librados-devel libcephfs-devel librgw-devel;

## start to build:

[root@localhost tmp]# rpmbuild --rebuild /tmp/nfs-ganesha-2.7.1-0.1.el7.src.rpm;
…...
Requires: /usr/bin/pkg-config
Processing files: nfs-ganesha-debuginfo-2.7.1-0.1.el7.aarch64
Provides: nfs-ganesha-debuginfo = 2.7.1-0.1.el7 nfs-ganesha-debuginfo(aarch-64) = 2.7.1-0.1.el7
Requires(rpmlib): rpmlib(FileDigests) <= 4.6.0-1 rpmlib(PayloadFilesHavePrefix) <= 4.0-1 rpmlib(CompressedFileNames) <= 3.0.4-1
Checking for unpackaged file(s): /usr/lib/rpm/check-files /root/rpmbuild/BUILDROOT/nfs-ganesha-2.7.1-0.1.el7.aarch64
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-mount-9P-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-vfs-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-proxy-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-lttng-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-rados-grace-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-nullfs-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-mem-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-gpfs-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-ceph-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-rgw-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-xfs-2.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/libntirpc-1.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/libntirpc-devel-1.7.1-0.1.el7.aarch64.rpm
Wrote: /root/rpmbuild/RPMS/aarch64/nfs-ganesha-debuginfo-2.7.1-0.1.el7.aarch64.rpm
Executing(%clean): /bin/sh -e /var/tmp/rpm-tmp.TR82uw
+ umask 022
+ cd /root/rpmbuild/BUILD
+ cd nfs-ganesha-2.7.1
+ /usr/bin/rm -rf /root/rpmbuild/BUILDROOT/nfs-ganesha-2.7.1-0.1.el7.aarch64
+ exit 0
Executing(--clean): /bin/sh -e /var/tmp/rpm-tmp.3cZjIh
+ umask 022
+ cd /root/rpmbuild/BUILD
+ rm -rf nfs-ganesha-2.7.1
+ exit 0

## check the build result, we see all nfs-ganesha aarch64 rpms are there:

[root@localhost tmp]# ls -l /root/rpmbuild/RPMS/aarch64/
total 9348
-rw-r--r--. 1 root root  107372 Jun  7 17:37 libntirpc-1.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   84656 Jun  7 17:37 libntirpc-devel-1.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root  586564 Jun  7 17:37 nfs-ganesha-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   26468 Jun  7 17:37 nfs-ganesha-ceph-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root 3752980 Jun  7 17:37 nfs-ganesha-debuginfo-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   49976 Jun  7 17:37 nfs-ganesha-gpfs-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   17948 Jun  7 17:37 nfs-ganesha-lttng-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   26004 Jun  7 17:37 nfs-ganesha-mem-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root    5372 Jun  7 17:37 nfs-ganesha-mount-9P-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   12528 Jun  7 17:37 nfs-ganesha-nullfs-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   39148 Jun  7 17:37 nfs-ganesha-proxy-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root    7576 Jun  7 17:37 nfs-ganesha-rados-grace-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   19864 Jun  7 17:37 nfs-ganesha-rgw-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   38672 Jun  7 17:37 nfs-ganesha-vfs-2.7.1-0.1.el7.aarch64.rpm
-rw-r--r--. 1 root root   37192 Jun  7 17:37 nfs-ganesha-xfs-2.7.1-0.1.el7.aarch64.rpm

[root@localhost tmp]# cd /root/rpmbuild/RPMS/aarch64/

## create tar.gz pkg for other arm64 NeoKylin/Centos Linux to use:

[root@localhost tmp]# tar -czvf nfs-ganesha-2.7.1.aarch64.rpm_all.tar.gz  *.aarch64.rpm

[root@localhost tmp]# ls -ltr | grep tar.gz
-rw-r--r--. 1 root root 4721190 Jun  7 18:19 nfs-ganesha-2.7.1.aarch64.rpm_all.tar.gz

## install ganesha rpms on any NeoKylin Linux or Centos/ Redhat OS with Arm64 Architect

[root@localhost tmp]# yum install -y libtirpc nfs-utils rpcbind;       ## install depends first
[root@localhost tmp]# tar -xzvf nfs-ganesha-2.7.1.aarch64.rpm_all.tar.gz;
[root@localhost tmp]# rpm Uvh *.rpm;          ## install nfs-ganesha rpms

Preparing...                          ################################# [100%]
Updating / installing...
   1:libntirpc-1.7.1-0.1.el7          ################################# [  7%]
   2:nfs-ganesha-2.7.1-0.1.el7        ################################# [ 13%]
   3:nfs-ganesha-ceph-2.7.1-0.1.el7   ################################# [ 20%]
   4:nfs-ganesha-gpfs-2.7.1-0.1.el7   ################################# [ 27%]
   5:nfs-ganesha-lttng-2.7.1-0.1.el7  ################################# [ 33%]
   6:nfs-ganesha-mem-2.7.1-0.1.el7    ################################# [ 40%]
   7:nfs-ganesha-nullfs-2.7.1-0.1.el7 ################################# [ 47%]
   8:nfs-ganesha-proxy-2.7.1-0.1.el7  ################################# [ 53%]
   9:nfs-ganesha-rados-grace-2.7.1-0.1################################# [ 60%]
  10:nfs-ganesha-rgw-2.7.1-0.1.el7    ################################# [ 67%]
  11:nfs-ganesha-vfs-2.7.1-0.1.el7    ################################# [ 73%]
  12:nfs-ganesha-xfs-2.7.1-0.1.el7    ################################# [ 80%]
  13:libntirpc-devel-1.7.1-0.1.el7    ################################# [ 87%]
  14:nfs-ganesha-mount-9P-2.7.1-0.1.el################################# [ 93%]
  15:nfs-ganesha-debuginfo-2.7.1-0.1.e################################# [100%]

Above rpm pkgs have been uploaded to link below for download :

https://github.com/benlu36/nfs-ganesha-2.7.1-0.1.el7.aarch64/;

I will try to upload them to ceph offical repostory later at:

http://download.ceph.com/nfs-ganesha/rpm-V2.7-stable/mimic/aarch64/;


– Benjamin Lu
