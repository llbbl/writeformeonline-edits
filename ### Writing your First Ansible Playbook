### Writing your First Ansible Playbook

Ansible is an open-source software provisioning, configuration management, and application-deployment tool. Developed by Ansible Community and Redhat. Ansible has been around for a quite while now, and it is a must tool for configuration management. Ansible platform is written in Python and it allows the users to script commands in YAML as an imperative programming paradigm. Ansible is designed for multi-tier deployment and does not manage one system at a time; it models IT infrastructure by describing all interrelated systems. Ansible is completely agentless, which means that it works by connecting nodes through ssh (Secure Shell), i.e, by default. It also gives a leverage of using other methods of connection, such as Kerberos. Ansible utilizes a push model, where the configuration is pushed from Ansible Control Node to its Managed Nodes, Unlike Tools like Puppet and Chef which work on Pull Model.

![](https://cdn-images-1.medium.com/max/1600/1*aH9avUEYpjafg7gvj0sA5g.jpeg)

So lets get into writing your first playbook without getting overwhelmed.

**Before writing a playbook, you need to understand what is a playbook ?**

Playbooks are Ansible's configuration, deployment, and orchestration language. They can describe a policy you want your remote systems to enforce, or a set of steps in a general IT process. So to Write playbooks you need to know the basics of YML or "Ain't Markup Language". Here's an basic yml example:

> ---
> countries:
>  - America
>  - China
>  - Canada
>  - India

The Above example show the we started the YML code with three dashes. The file starts with three dashes. These dashes indicate the start of a new YAML document. YAML supports multiple documents, and compliant parsers will recognize each set of dashes as the beginning of a new one.

Next, we see the construct that makes up most of a typical YAML document: a key-value pair. **Countries** is a key that points to four values : America, China,Canada,India. Sound's similar to a dictionary right. Note: The Key is seperated to a value by colon (:). A string value is written in "" and and a number without quotes. For multiple values for a key, you can distribute by adding (-) hyphen after each value. See Below example for more Clarity. Here's another example with different key:value pairs:

**---
 doe: "a deer, a female deer"
 ray: "a drop of golden sun"
 pi: 3.14159
 xmas: true
 french-hens: 3

 calling-birds:
   - huey
   - dewey
   - louie
   - fred

 xmas-fifth-day:
   calling-birds: four
   french-hens: 3
   golden-rings: 5
   partridges:
     count: 1
     location: "a pear tree"
   turtle-doves: two

So now you are somewhat familiar with YAML syntax. You can now dive into Ansible Playbooks. Ansible Playbooks mainly consists of

1.  Tasks
2.  Hosts
3.  Handlers
4.  Variables
5.  Modules

Let's take an example of complete apache installation using Ansible Playbook and understand each step:
>---
- name: This sets up an httpd webserver
  hosts: webserver
  vars:
    - greetings: Your Http Server is being Installed
  tasks:
  - name: Install apache packages 
    yum:
      name: httpd
      state: present
    notify:
       start httpd
  handlers:
    - name: start httpd
      service:
        name: httpd
        state: started
      debug:
        msg: "{{ greetings }}"
  - name: Open port 80 for httpd access
    firewalld:
      service: httpd
      permanent: true
      state: enabled
  - name: Restart the firewalld service to load the changes
    service: 
      name: firewalld 
      state: restarted

The First **name** defines the name of Playbook. The **hosts** parameter define the hosts system to apply configuration. I have written webserver here. Now to resolve the DNS name you need to apply entry into **/etc/ansible/hosts** file for your host system as follows:
>---
webserver=10.0.1.150
RHEL=10.0.1.151
# RHEL is hostname given and ansible_host for the for ip allocation**

**2)** **Tasks:**

In tasks you define which configuration changes you want. Everything is defined here whether you want to update a service or copy a file to host, tasks is where you revolve.

Tasks are executed in order, one at a time, against all machines matched by the host pattern, before moving on to the next task. It is important to understand that, within a play, all hosts are going to get the same task directives. It is the purpose of a play to map a selection of hosts to tasks.

When running the playbook, which runs top to bottom, hosts with failed tasks are taken out of the rotation for the entire playbook. If things fail, simply correct the playbook file and rerun.

* * * * *

**3) Handlers**:

Handlers are just like normal [tasks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#tasks-list) in an Ansible playbook but they run only when if the Task contains a "notify" directive. It also indicates that it changed something.

Regardless of how many tasks notify a handler, it will run only once, after all of the tasks completed in a particular play.

You can have several tasks calling multiple handlers as per your need.

The handlers is executed by Ansible right after httpd is installed and the service

* * * * *

**4) Variables:**

Ansible supports the use of variables to better customize the execution of tasks and playbooks. This way, it's possible to use the same playbook with different targets and environments.

Variables can come from different sources, such as the playbook file itself or external variable files that are imported in the playbook. [Special precedence rules](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) will apply when working with multiple variable sources that define a variable with the same name.

The `vars` section of the playbook defines a list of variables that will be injected in the scope of that play. All tasks, as well as any file or template that might be included in the playbook, will have access to these variables.

**5) Modules:**

Ansible ships with a number of modules (called the 'module library') that can be executed directly on remote hosts or through Playbooks.

You can also write their own modules. These modules can control system resources, like services, packages, or files (anything really), or handle executing system commands.

Some examples of ansible modules:

In below example we would be executing "/sbin/reboot -t now" on the linux machine

- name: reboot the servers\
  command: /sbin/reboot -t now

Another example of restarting the httpd service

- name: restart webserver\
  service:\
    name: httpd\
    state: restarted

In our complete ansible code, we have used **yum module **which is used to execute the yum command in a linux machine

**service module **is used to restart,stop or start a service.

**firewalld module** is used to execute actions for the firewalld. Some examples are allowing a port or service

Complete list of ansible modules in Ansible 2.9 is [here](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)

* * * * *

Lastly running your ansible playbook using :

ansible-playbook myhttpdserver.yml

This will apply the playbook to all the hosts in the **webserver** group in the inventory. You will be given a report showing the status of each host and the tasks that had been run.