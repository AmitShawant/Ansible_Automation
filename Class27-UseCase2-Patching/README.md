# How To patch linux servers using Ansible

## 1: Linux Patching
-  **Have you ever wondered how to patch your systems, reboot, and continue working?:** If so, you'll be interested in Ansible, a simple configuration management tool that can make some of the hardest work easy. For example, system administration tasks that can be complicated, take hours to complete, or have complex requirements for security.
In my experience, one of the hardest parts of being a sysadmin is patching systems. Every time you get a Common Vulnerabilities and Exposure (CVE) notification or Information Assurance Vulnerability Alert (IAVA) mandated by security, you have to kick into high gear to close the security gaps. (And, believe me, your security officer will hunt you down unless the vulnerabilities are patched.)
So let’s get started.

-  **Ansible ** can reduce the time it takes to patch systems by running packaging modules. To demonstrate, let's use the yum module to update the system. Ansible can install, update, remove, or install from another location (e.g., rpmbuild from continuous integration/continuous development). Here is the task for updating the system:

## 4: Prerequisites

1.	**server.cnl.com** – 1 CPU – 1GB RAM (Python 2.7) - **Ansible Server**
2.	**node1.cnl.com** – 1 CPU – 1GB RAM ( python 2.6 and above) - **Ansible Client 1**
3.	**node2.cnl.com** – 1 CPU – 1GB RAM ( python 2.6 and above) - **Ansible Client 2**

from ansible server login as an ansible user as per class 4.From ansible user execute below command

ansible all -m ping

this above ping command should return with ping / pong green color.





## scenario 1: Linux patching


---
- name: " This playbook is to apply patches to linux servers"
  hosts: all
  tasks:
  - name: install httpd
    yum:
     name: httpd
     state: latest
  - name: "Check if reboot required"
    shell: KERNEL_NEW=$(rpm -q --last kernel |head -1 | awk '{print $1}'|sed 's/kernel-//'); KERNEL_NOW=$(uname -r); if [[ $KERNEL_NEW != $KERNEL_NOW ]]; then echo "reboot needed"; else echo "reboot not needed"; fi
    ignore_errors: true
    register: reboot_status
  - name: "This play is to restart the system using above status check"
    command: shutdown -r +1 "Rebooting System After Patching"
    async: 0 #Maximum allowed time
    poll: 0  #The poll keyword accepts a numeric value to know how many seconds it should wait before polling or following up with the long-running async task 10 sec by default
    when: reboot_status.stdout == "reboot needed"
    register: reboot_started
    ignore_errors: true
  - name: "This play will wait for 1 minutes for system to come up"
    pause: minutes=1

    # Now we will run a local 'ansible -m ping' on this host until it returns.
    # This works with the existing ansible hosts inventory and so any custom ansible_ssh_hosts definitions are being used
  - name: check the system status
    local_action: shell ansible -u ansible -m ping {{ inventory_hostname }}
    register: result
    until: result.rc == 0
    retries: 30
    delay: 10

    # And finally, execute 'uptime' when the host is back.
  - name: check the client uptime
    shell: uptime
~
~
~


```
ansible-playbook patch.yml
```

