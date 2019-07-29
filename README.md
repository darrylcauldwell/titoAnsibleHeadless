This repository contains two Ansible playbooks which can be used to install the [Time Tracking Overview (TiTO) application](https://github.com/vmeoc/Tito).

## Template Creation

These playbooks are written to be used on a CentOS 7 template. As Ansible is used on every VM I included this in the template and as this is for demonstration purposes also disabled the guest firewall. To create the template I used the following commands to an clean VM.
```bash
yum update
yum install cloud-init
yum install epel-release.noarch
yum install ansible
systemctl disable firewalld
shutdown -h 0
```
