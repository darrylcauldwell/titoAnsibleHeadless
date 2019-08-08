# TiTO Ansible Headless

This repository contains two [Ansible playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html) that can be used to install the [Time Tracking Overview (TiTO) application](https://github.com/vmeoc/Tito). The TiTO application has a simple two tier architecture, the frontend uses Apache to host a PHP website and the backend is a MySQL database.

It also contains a VMware Cloud Automation Services blueprint which builds two VMs with Ansible installed. The blueprint then instructs each VM to pull and execute the correct playbooks to install the application to the correct tier.

## TiTO Ansible Headless Cloud Assembly Blueprint

The TiTO Ansible Server blueprint depends on a deployment environment configured in Cloud Assembly with some explicit properties. The explicit properties can be changed in the blueprint to match environment.

1. A valid Cloud Zone tagged with target:vsphere 
2. A Ubuntu 16.04 VM template with cloud-init installed and working in Cloud Zone with Image Mapping named im-ubuntu16046
3. A Flavor Mapping named fl-small with 2x CPU and 4GB RAM
4. A valid Network Profile with internet connectivity in Cloud Zone tagged with environment:production

The TiTO Cloud Assembly blueprint is a file stored in this repository named [tito-ansibleHeadless.yml](https://raw.githubusercontent.com/darrylcauldwell/titoAnsibleHeadless/master/tito-ansibleHeadless.yml). Create a new Cloud Assembly blueprint and copy paste the contents of the file into the new blueprint.

You should now be able to deploy the blueprint. Once it completes successfully you should be able to point web browser to IP of web server and get TiTO.

* Troubleshooting Tip 1, Cloud-init is fire and forget once it has issued the command to start the Ansible playbook it shows as deployment completed in Cloud Assembly. The Ansible playbooks take a little while to run as they pull down packages from internet, install and configure them. When demonstrating if you have open remote consolve to the web VM you can see the Ansible playbook output to the console and see when Ansible playbook finishes. You will only be able to browse TiTO application once Ansible playbook is completed.

* Troubleshooting Tip 2, the Ansible playbook is written to install application on either Ubuntu16.04 or CentOS7. It uses conditional logic to do this so you will see some tasks skipped,  this is normal.