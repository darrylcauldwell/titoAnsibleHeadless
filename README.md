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
