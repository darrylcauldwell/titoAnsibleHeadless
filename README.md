This repository contains two Ansible playbooks which can be used to install the [Time Tracking Overview (TiTO) application](https://github.com/vmeoc/Tito).

## Template Creation

These playbooks are written to be used on both CentOS 7.6 and Ubuntu 16.04.6 VM templates. As Ansible is used on every VM I included this in the template and as this is for demonstration purposes also disabled the guest firewall. To create the template I used the following commands to an clean VM.

### CentOS 7.6

```bash
yum update
yum install cloud-init
yum install epel-release.noarch
yum install ansible
systemctl disable firewalld
shutdown -h 0
```

### Ubuntu

** This does not appear to work, when deployed with CAS the template fails to connect to network,  presume cloud-init getting stuck **
[Building a CAS ready Ubuntu template for vSphere](https://blogs.vmware.com/management/2019/02/building-a-cas-ready-ubuntu-template-for-vsphere.html) 
** This does not appear to work, when deployed with CAS the template fails to connect to network,  presume cloud-init getting stuck **

I think the issue is related to [this bug](https://bugs.launchpad.net/ubuntu/+source/open-vm-tools/+bug/1793715) which is behing [this kb article](https://kb.vmware.com/s/article/56409).

In template we should enable root access as if cloud-init fails ubuntu user cannot logon.

```bash
sudo su
passwd
vi /etc/ssh/sshd_config

ensure PermitRootLogin yes
```

Install cloud init

```
apt -y install cloud-init
cloud-init clean
```

Open the tmp.conf under /usr/lib/tmpfiles.d/tmp.conf file, go to the line 11 and add the prefix # .

```
vi /usr/lib/tmpfiles.d/tmp.conf

#D /tmp 1777 root root -
```

Open the /lib/systemd/system/open-vm-tools.service file and add this line “After=dbus.service” under [Unit].

```
vi /lib/systemd/system/open-vm-tools.service

[Unit]
After=dbus.socket
```
