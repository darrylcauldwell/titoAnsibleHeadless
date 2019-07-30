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


[Building a CAS ready Ubuntu template for vSphere](https://blogs.vmware.com/management/2019/02/building-a-cas-ready-ubuntu-template-for-vsphere.html) 

** The steps in blog post do not appear to give a usable CAS template VM.  When attempting to deploy blueprint the deployment hangs, it appears as though the VM guest customization does not run as the VM network doesn't get connected.


I think the issue is related to [this bug](https://bugs.launchpad.net/ubuntu/+source/open-vm-tools/+bug/1793715) which is behing [this kb article](https://kb.vmware.com/s/article/56409).

In template we should enable root access as if cloud-init fails ubuntu user cannot logon so I enable root SSH for troubleshooting.

```bash
sudo su
passwd
vi /etc/ssh/sshd_config

ensure PermitRootLogin yes
```

Then do the normal cloud init install.

```
apt -y install cloud-init
```

Follow steps in Ubunuty bug report to force to use cloud-init not perl scripts

```
vi /etc/cloud.cfg

disable_vmware_customization: false
```

I then follow a mix of steps in KB and Ubuntu bug report to add dbus socket dependancy to VMware tools.

```
vi /usr/lib/tmpfiles.d/tmp.conf

#D /tmp 1777 root root -

vi /lib/systemd/system/open-vm-tools.service

[Unit]
After=dbus.socket
```
