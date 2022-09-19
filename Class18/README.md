# Ansible Jinja2 Template

## Step-1: What is Jinja2 template
-  **Jinja2:** Jinja2 templates are simple template files that store variables that can change from time to time. When Playbooks are executed, these variables get replaced by actual values defined in Ansible Playbooks. This way, templating offers an efficient and flexible solution to create or alter configuration file with ease.

## Step-2: Jinja2
 - **What is Template:** A template is a file that contains all your configuration parameters, but the dynamic values are given as variables in the Ansible. During the playbook execution, it depends on the conditions such as which cluster you are using, and the variables will be replaced with the relevant values.
 
 - **Ansible Use ?:** Ansible uses Jinja2 templating to enable dynamic expressions and access to variables. Ansible includes a lot of specialized filters and tests for templating. ... All templating happens on the Ansible controller before the task is sent and executed on the target machine.
 
 - **Jinja Format?:** A Jinja template is simply a text file. Jinja can generate any text-based format (HTML, XML, CSV, LaTeX, etc.).xml , or any other extension is just fine. A template contains variables and/or expressions, which get replaced with values when a template is rendered; and tags, which control the logic of the template.


To start this handson lab,you need following resources.



## Step-3: Prerequisites

1.	**server.cnl.com** – 1 CPU – 1GB RAM (Python 2.7) - **Ansible Server**
2.	**node1.cnl.com** – 1 CPU – 1GB RAM ( python 2.6 and above) - **Ansible Client 1**
3.	**node2.cnl.com** – 1 CPU – 1GB RAM ( python 2.6 and above) - **Ansible Client 2**

from ansible server login as an ansible user as per class 4.From ansible user execute below command

ansible all -m ping

this above ping command should return with ping / pong green color.



## scenario 1: Jinja2 file with just copy module

- create file called hello_world.j2 [vim hello_world.j2].
```
{{ variable_to_be_replaced }}
This line won't be changed
Variable given as inline - {{ inline_variable }} - :)
```
- save this hello_world.j2 file

- create file called file.yml [vim file.yml].
```
- hosts: all
  vars:
    variable_to_be_replaced: 'Hello world'
    inline_variable: 'hello again'
  tasks:
    - name: Ansible Template Example
      copy:
        src: hello_world.j2
        dest: /var/tmp/hello_world.txt
```
- save this file.yml file

- Execute the ansible playbook

```
ansible-playbook file.yml
```

- Verify the file from ansible client machines

```
ansible all -m command -a "cat /var/tmp/hello_world.txt"
```




## scenario 2: Jinja2 file with template module

- create file called hello_world.j2 [vim hello_world.j2].
```
{{ variable_to_be_replaced }}
This line won't be changed
Variable given as inline - {{ inline_variable }} - :)
```
- save this hello_world.j2 file

- create file called file.yml [vim file.yml].
```
- hosts: all
  vars:
    variable_to_be_replaced: 'Hello world'
    inline_variable: 'hello again'
  tasks:
    - name: Ansible Template Example
      template:
        src: hello_world.j2
        dest: /var/tmp/hello_world.txt
```
- save this file.yml file

- Execute the ansible playbook

```
ansible-playbook file.yml
```

- Verify the file from ansible client machines

```
ansible all -m command -a "cat /var/tmp/hello_world.txt"
```

- **Now you see the differences right between just copy module and template module :) **


## Ansible template with_items for multiple files


## scenario 3: Jinja2 file to collect entire servers inventory

- If you dont know the factors name then run the below command and paste in notepad

```
ansible node1 -m setup
```

- Now create jinja2 file like below.[vim inventory.j2].

```
My name is {{ ansible_nodename }}.My Ip Addresss is {{ ansible_eth0.ipv4.address }}.My Operating system is {{ ansible_distribution }}.My Operating System version is {{ ansible_distribution_version}}.I am belongs to {{ ansible_os_family }}.I have {{ ansible_processor_cores }} cores.I have {{ ansible_processor_count }} processor.I am {{ ansible_machine }} architecture.My kernel version is {{ ansible_kernel }}.
```

- create file called file.yml [vim file.yml].
```
- hosts: all
  tasks:
    - name: Ansible template inventory collection example.
      template:
        src: inventory.j2
        dest: /var/tmp/inventory.txt
        mode: 0777
```


- Execute the ansible playbook

```
ansible-playbook file.yml
```

- Verify the file from ansible **client machines**

```
ansible node1 -m command -a "cat /var/tmp/node1_inventory.txt"
ansible node2 -m command -a "cat /var/tmp/node2_inventory.txt"
```
