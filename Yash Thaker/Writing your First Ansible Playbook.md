# Writing your First Ansible Playbook
An Intro to Ansible with Examples to Help You Get Started

![](https://cdn-images-1.medium.com/max/1600/1*aH9avUEYpjafg7gvj0sA5g.jpeg)


## What is Ansible

Ansible is an open-source software provisioning, configuration management, and application-deployment tool. It is written in Python, being developed by the Ansible Community and Redhat. 

With Ansible, you can manage many different systems from one central system. However, Ansible is entirely agentless, which means that it works by connecting nodes through ssh (Secure Shell), i.e., by default.

Unlike Puppet and Chef, which work on the pull model, Ansible utilizes a push model, where the configuration is pushed out to its managed nodes. 

## Getting Started with YAML and Playbooks

So let's get into writing your first Playbook without getting overwhelmed.

**What is a Playbook?**

Playbooks are Ansible's configuration, deployment, and orchestration language. They can describe a policy you want your remote systems to enforce or a set of steps in a general IT process. So to write playbooks, you need to know the basics of YML or "Ain't Markup Language  ."Here's a basic YML example:

```yaml

---
countries:
  - America
  - China
  - Canada
  - India  

```


The above example shows that we started the YML code with three dashes. The file starts with three dashes. These dashes indicate the start of a new YAML document. YAML supports multiple documents, and compliant parsers will recognize each set of dashes as the beginning of a new one.

Next, we see the construct that makes up most of a typical YAML document: a key-value pair. 

**Countries** is a key that points to four values: America, China, Canada, and India. 

**Note:** The Key is separated to a value by a colon (:). A string value is written in  double-quotes and a number without quotes. 

You can distribute multiple values for a key by adding a (-) hyphen after each value. Here's another example with different key: value pairs:


```yaml

---
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
```

So now you are somewhat familiar with YAML syntax. You can now dive into Ansible Playbooks. Ansible Playbooks mainly consist of the following types of concepts.

	1. Tasks
	2. Handlers
	3. Variables
	4. Modules


Let's take an example of a complete apache installation using Ansible Playbook and understand each step:


```yaml
  
---
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
```

The First **name** defines the name of Playbook. The **hosts** parameter define the host's system to apply the configuration. For example, to resolve the name 'web server,' we will need to update the **/etc/ansible/hosts** file with your system IP as follows:

  ```yaml
---
webserver=1.2.3.4

```


### 1) Tasks:

In tasks, you define which configuration you changes want. Whether you want to update a service or copy a file to a host, tasks are where you revolve.

Tasks are executed in order, one at a time, against all machines matched by the host pattern before moving on to the next task. It is important to understand that, within a play, all hosts will get the same task directives. This is because the purpose of a play is to map a selection of hosts to tasks.

When running the Playbook, which runs top to bottom, hosts with failed tasks are removed from the rotation for the entire Playbook. If things fail, correct the playbook file and rerun.

### 2) Handlers:

Handlers are just like normal [tasks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#tasks-list) in an Ansible playbook, but they run only when the Task contains a "notify" directive. It also indicates that it changed something.

Regardless of how many tasks notify a handler, it will run only once after completing all tasks in a particular play.

You can have several tasks calling multiple handlers as per your need.

The handlers are executed by Ansible right after httpd is installed and the service.


### 3) Variables:

Ansible supports using variables to customize the execution of tasks and playbooks better. This way, it's possible to use the same Playbook with different targets and environments.

Variables can come from different sources, such as the playbook file itself or external variable files imported into the Playbook. [Special precedence rules](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) will apply when working with multiple variable sources that define a variable with the same name.

The `vars` section of the Playbook defines a list of variables that will be injected into the scope of that play. All tasks and any file or template that might be included in the Playbook will have access to these variables.

### 4) Modules:

Ansible ships with several modules (called the 'module library) that can be executed directly on remote hosts or through Playbooks.

You can also write your modules. These modules can control system resources, like services, packages, or files (anything really), or handle executing system commands.

Some examples of ansible modules:

In the below example, we would be executing "/sbin/reboot -t now" on the Linux machine.

```yaml
- name: reboot the servers
  command: /sbin/reboot -t now
```

Another example of restarting the httpd service

```yaml
- name: restart webserver
  service:
	name: httpd
	state: restarted
```

In our complete ansible code, we have used **yum module**, which is used to execute the yum command in a Linux machine.

**service module** is used to restart, stop or start a service.

**firewalld module** is used to execute actions for the firewalld. 

The complete list of ansible modules in Ansible 2.9 is [here](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)

### Final Step - Running Your Playbook

You run your Ansible Playbook with the following command :

```ansible-playbook myhttpdserver.yml```

This will apply the Playbook to all the hosts in the **webserver** group in the inventory. In addition, you will be given a report showing the status of each host and the tasks that have been run.
