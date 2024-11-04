## Step 1 - Jenkins job Enhancement

let us make some changes to our Jenkins job - now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job - we will require Copy Artifact plugin.

1. Go to Jenkins-Ansible server and create a new directory called ansible-config-artifact - we will store there all artifacts after each build.

```
sudo mkdir ansible-config-artifact
```

2. Change permissions to this directory, so Jenkins could save files there -

```
 chmod -R 0777 /home/ubuntu/ansible-config-artifact
```

3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins.

![image](https://github.com/user-attachments/assets/50e7ecd1-9e05-441b-92cc-7e6b6d037d39)


4. Create a new Freestyle project and name it save_artifacts.

![image](https://github.com/user-attachments/assets/5a67c46c-7fde-46be-950d-fb3e67af05dd)


5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:

![image](https://github.com/user-attachments/assets/79c5efdc-2f35-49e9-ac9a-10713fe0f36c)

note: We can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

![image](https://github.com/user-attachments/assets/12753564-14c6-4967-8b4e-c653807b9b8b)

![image](https://github.com/user-attachments/assets/b5c97f2c-e438-42aa-8721-091849999a53)

![image](https://github.com/user-attachments/assets/0879e027-d72c-421f-9355-b4592fb86c51)



7. Test set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside main branch).

Doing changes in ansible-config-mgt it will goint to trigger the ansible jobs due to Webhook which we Already setup.

![image](https://github.com/user-attachments/assets/cae78794-746a-4a4e-bc03-48bb7f77a9db)


It will goint to trigger ansible once its get success it will going to trigger the save_artifacts.

We can artifact copied to ansible-config-artifact/ directory.
![image](https://github.com/user-attachments/assets/461372c3-c4c8-4e8e-a78f-9b773a033e24)



## Step-2 -Refactor Ansible by importing other playbooks into site.yml
- Setting Up for Refactoring.
    - Ensure you have pulled the latest code from the master (main) branch. This is best practice and to ensure your project is always updated with the latest code.
      ```
      git pull origin <branch>
      ```
    - Create a new branch and name it refactor
      ```
      git branch refactor
      ```
    - Select the created refactor branch.
      ```
      git checkout refactor
      ```
![image](https://github.com/user-attachments/assets/75606edd-336f-4511-a093-46ed416175af)


-  Create a site.yml file in the playbooks folder. This will serve as the entry point to all infrastructer configurations.

![image](https://github.com/user-attachments/assets/c0c18572-4192-40c0-8d09-dfdfe91751ea)


-  Create a static-assignments folder at the root of the repository for organizing child playbooks.

   [image](https://github.com/user-attachments/assets/47181ade-244f-437e-b989-999f4ee2d8e0)

-  Move common.yml file into the newly created static-assignments folder.

 ![image](https://github.com/user-attachments/assets/fce0d696-299e-4e1e-87ed-5a158f49c5e0)


-  Inside site.yml file, import common.yml playbook.

  ```
  ---
  - hosts: all
  - import_playbook: ../static-assignments/common.yml
 
  ```
  The code above uses built in import_playbook Ansible module.
  
-  Folder structure should look like this;
```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
```
![image](https://github.com/user-attachments/assets/18ee177d-2dc1-4b83-8964-dc49f0d44b3a)


-  Run ansible-playbook command against the dev environment.

Push to your github repo and create a pull request, carefully check codes and merge into main branch.
![image](https://github.com/user-attachments/assets/0a5a46be-3aa5-4d4b-9e96-f0661c8c861c)



New Branch Got updated on remote repository.
![image](https://github.com/user-attachments/assets/3dfe22c6-e2ec-4fe7-ae02-bcdb2f1cc47c)

merging the code through PR (Pull Request.)

![image](https://github.com/user-attachments/assets/5085ce3e-18cb-4e38-b17d-db11f7d4edf0)


![image](https://github.com/user-attachments/assets/2f4428d2-d202-4b59-87f0-548c939104de)

Access your ansible-jenkins server, navigate to the directory where the artifacts are saved and run the playbook command again.

````
cd /home/ubuntu/ansible-config-artifact/
````
![image](https://github.com/user-attachments/assets/ff42958d-0803-44b9-b9f7-926cde28487a)

```
ansible-playbook -i inventory/dev.yml playbooks/site.yml
```
Output 1
![image](https://github.com/user-attachments/assets/cda16005-9ad5-41a9-aa84-00ead41a5fb1)

Output 2
![image](https://github.com/user-attachments/assets/a39a98c0-cc27-4996-8c10-e679cb9424b4)


-  Create another playbook common-del.yml under static-assignments for deleting Wireshark. inside it, place the following code in it and save

```
---- 
name: update web, nfs and db servers
hosts: webservers, nfs, db
remote_user: ec2-user
become: yes
become_user: root
tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: delete wireshark
      apt:
        name: wireshark-qt
        state: absent
        autoremove: yes
        purge: yes
        autoclean: yes
```
![image](https://github.com/user-attachments/assets/885c052b-2f6c-4939-bc2e-efbcbb46ba29)

update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

Login to Ansible-jenkins server. File common-del.yml got updated in ansible-config-artifact.
![image](https://github.com/user-attachments/assets/da16bf43-34cc-4435-9a88-a3ebbfecfbdd)

```
         cd /home/ubuntu/ansible-config-artifact/
         ansible-playbook -i inventory/dev.yml playbooks/site.yml
```
![image](https://github.com/user-attachments/assets/75c960d6-8900-4adf-a127-b8e293f4d3fc)

- Ensure wireshark is deleted on all servers by running wireshark --version

Output.
![image](https://github.com/user-attachments/assets/109a112f-dd8b-49a3-91bd-3aacc259e7e1)

Output.
![image](https://github.com/user-attachments/assets/755061aa-3cab-4b93-90cb-2808a7ab5843)

Output.
![image](https://github.com/user-attachments/assets/743da5db-e8f9-4e84-9d43-dada06e9607e)

Output.
![image](https://github.com/user-attachments/assets/def71fc4-dbb0-4de0-b703-b332d443811c)

Output.
![image](https://github.com/user-attachments/assets/bbe85f45-5b13-4dc6-ae94-5b455ac8e728)

## 3- Configure UAT Webservers with a role 'Webserver'
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

## Step-4 Reference Webserver role.

Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.

![image](https://github.com/user-attachments/assets/c22d99d1-11dd-4b6c-8d42-583620db4e85)


- Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml. So, we should have this in site.yml

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml

```
![image](https://github.com/user-attachments/assets/f294437a-4aa5-401f-a4d1-d18b0f2fbb9b)

 ## Step 5: Commit & Test

- Commit your changes to your Git repository.
- Create a Pull Request and merge it into the main branch.

![image](https://github.com/user-attachments/assets/84c399a3-8474-4ea3-9dcb-2d192763c7cc)


- Access your ansible-jenkins server and navigate to the ansible-config-artifact directory.
- Run the playbook command.

```
  cd /home/ubuntu/ansible-config-artifact
  ansible-playbook -i inventory/uat.yml playbooks/site.yml
```

![image](https://github.com/user-attachments/assets/5c002000-f7ba-4ae0-a53f-8c8d5e117bcd)

![image](https://github.com/user-attachments/assets/7ee8edf1-c487-44f3-baff-02c49034ced1)
You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

UAT-WebServer1
![image](https://github.com/user-attachments/assets/8dbb27d3-9039-4267-a682-9301aa8ee70d)

UAT-WebServer2
![image](https://github.com/user-attachments/assets/a418dbd9-6749-4e8a-b93f-0eed115ff6b0)


Our Ansible architecture now looks like this:

![image](https://github.com/user-attachments/assets/14e81f4a-a1eb-4fd4-8e8e-104fe64faa69)

## Conclusion:

- This Ansible project demonstrates a well-structured approach to configuration management and infrastructure as code. The repository showcases several key Ansible best practices and advanced concepts:

- Refactoring: The project illustrates the process of refactoring Ansible code, which is crucial for maintaining clean, efficient, and scalable automation scripts.
- Static Assignments: By utilizing static assignments, the project provides a clear and predictable way to manage configurations across different environments or server groups.
- Role-based structure: The use of roles (as seen in the 'roles' directory) promotes code reusability and modularity, making it easier to manage complex configurations.
- Environment-specific configurations: The project separates configurations for different environments (dev, uat, prod), allowing for tailored setups across various stages of deployment.
- Inventory management: The project includes inventory files, showcasing how to organize and group hosts for different environments or purposes.
Playbook organization: Multiple playbooks are used for different purposes, demonstrating how to break down complex automation tasks into manageable, focused scripts.
- Variable management: The use of variable files (like in the 'env-vars' directory) shows how to manage environment-specific variables effectively.
CI/CD Integration: The presence of a Jenkinsfile suggests that this project is integrated into a continuous integration/continuous deployment pipeline, showcasing how Ansible can be part of a broader DevOps workflow.
- Overall, this project serves as an excellent example of how to structure a medium to large-scale Ansible project. It demonstrates key concepts in configuration management, showcases best practices in Ansible usage, and provides a template that could be adapted for various infrastructure automation needs. The refactoring aspect particularly highlights the importance of continuously improving and optimizing automation scripts for better maintainability and efficiency.

