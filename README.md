This repository contains two Ansible playbooks which can be used to install the [Time Tracking Overview (TiTO) application](https://github.com/vmeoc/Tito).

It also contains a VMware Cloud Automation Services blueprint which builds two VMs with Ansible installed. The blueprint then instructs each VM to pull and execute the correct playbooks to install the application to the correct tier.

## Cloud Assembly - Cloud Zone Setup

1. Deploy a cloud proxy appliance to vSphere environment (Infrastructure > Connections > Cloud Proxies)

2. Create the following tags (Infrastructure > Configure > Tags)

Key | Value
--- | ---
cloud | vsphere
env | home-dc
net | home-net
  
3. Create a vCenter Cloud Zone (Infrastructure > Connections > Cloud Zone) the proxy deployed previously will communicate with vCenter.  Applying the 'cloud:vsphere' and 'env:home-dc' Capability Tag to the Cloud Zone allows the blueprint to filter and select the correct Cloud Zone to deploy.

## vSphere Template Creation

These playbooks are written to be used on both CentOS 7.6 and Ubuntu 16.04.6 VM templates. Cloud-init is used by Cloud Assembly to perform its configuration so this is required in the template. For this usecase as Ansible is used on every VM I included this and disabled the guest firewall in the templates. 

### CentOS 7.6

I create a VM with 2x CPU and 4GB RAM map the ISO as CDROM boot and run the following commands.

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


I think the issue is related to [this bug](https://bugs.launchpad.net/ubuntu/+source/open-vm-tools/+bug/1793715) which is explained [this kb article](https://kb.vmware.com/s/article/56409).

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

Reconfigure cloud-init to only run OVF.

```bash
dpkg-reconfigure cloud-init
```

Follow [these steps](https://kb.vmware.com/s/article/59557) to force to use cloud-init 

```
vi /etc/cloud/cloud.cfg

disable_vmware_customization: false
```

As some processes power VM with disconnected network this can delay change network config from `auto ens34` to `allow-hotplug en34`

```
vi /etc/network/interfaces

allow-hotplug ens34
```

Then clean and shutdown

```
cloud-init clean
shutdown -h 0
```

## Create Image Mapping

Once the templates are created in vSphere we must poll vCenter to sync these new templates to Cloud Assembly (Infrastructure > Connections > Cloud Zone > Sync Images).

We can now create an image mapping (Infrastructure > Configuration > Image Mappings). In the example blueprint I use the image mapping names 'ansibleCentOS76' and 'ansibleUbuntu1804'.

## Create Flavor Mapping

We can now create an flavor mapping (Infrastructure > Configuration > Flavor Mappings). In the example blueprint I use 2CPU and 4GB RAM and use the flavor mapping name 'ansibleMedium'.

## Create Network Profile

We can now create an network mapping (Infrastructure > Configuration > Network Profile). In the example blueprint, I use the network profile mapping tag 'net:home-net' and map profile to a VSS port group which has DHCP enabled and can route directly to the Internet.

## Create Cloud Assembly Project

We can now create a Project and associate this with the Cloud Zone (Infrastructure > Configuration > Projects).

## Create Blueprint

We can now create a Blueprint called 'ansibleHeadlessTito' and copy in the contents of the blueprint.yml from this repository.

## Deployment

Once the blueprint is deployed it should have created two VMs. If you browse to the IP of the web server you should be able to use the TiTO application.
