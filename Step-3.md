## Step 3 - Configure UAT Webservers with a role 'Webserver'

We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly - Web1-UAT and Web2-UAT.

![image](https://github.com/user-attachments/assets/96e59e8d-8808-4490-b5e0-3c1af025c3da)
Tip: Do not forget to stop EC2 instances that We are not using at the moment to avoid paying extra. For now, We only need 2 new RHEL 8 servers as Web Servers and 1 existing Jenkins-Ansible server up and running.

2. To create a role, We must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

There are two ways how you can create this folder structure:

- Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)

```
mkdir roles
cd rolesansible-galaxy
init webserver
```
- Create the directory/files structure manually.

You can create the Ansible role using either the ansible-galaxy command or manually: However , since we use github as version control, it is advisable to manually create the roles directory and the chikd fikes in it, so in your vs code terminal, run the following code to create the roles directory and child files

```
            mkdir roles
            cd roles
            mkdir webserver
            cd webserver
            touch README.md
            mkdir defaults, handlers, meta, tasks, templates
```
* Navigate into each of the created directories and create a main.yml file in each directory
![image](https://github.com/user-attachments/assets/39236f37-ef39-4e86-956d-bc15e3ad14a0)

The entire folder structure should look like below, but if we create it manually - we can skip creating tests, files, and vars or remove them if we used ansible-galaxy.

3. Update your uat.yml at "ansible-config-mgt/inventory/uat.yml" with the IP addresses of your two UAT Web servers:

``
         [uat-webservers]
         <Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
         <Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
``
![image](https://github.com/user-attachments/assets/b43f75bf-f799-496e-90ab-ef28f26e2966)  


5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

- Install and configure Apache (httpd service)
- Clone Tooling website from GitHub https://github.com//tooling.git.
- Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
- Make sure httpd service is started

Your main.yml consist of following tasks:

```
# tasks file for webserver
---
- name: install apache
  remote_user: ec2-user
  become: true
  become_user: root
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  remote_user: ec2-user
  become: true
  become_user: root
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  remote_user: ec2-user
  become: true
  become_user: root
  ansible.builtin.git:
    repo: https://github.com/mimi-netizen/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  remote_user: ec2-user
  become: true
  become_user: root
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  remote_user: ec2-user
  become: true
  become_user: root
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  remote_user: ec2-user
  become: true
  become_user: root
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
![image](https://github.com/user-attachments/assets/da5845a1-df66-40b8-8359-894c7cdc3fe2)
