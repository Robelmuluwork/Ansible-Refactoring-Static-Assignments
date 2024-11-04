### Step 2 - Refactor Ansible code by importing other playbooks into site.yml

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
      ![image](https://github.com/user-attachments/assets/b24f78e2-90d8-4e9a-8fa9-abcb89fd9876)

-  Create a site.yml file in the playbooks folder. This will serve as the entry point to all infrastructer configurations.

   ![image](https://github.com/user-attachments/assets/a1166aad-6d94-494b-9c82-0f449a3e9bc1)

-  Create a static-assignments folder at the root of the repository for organizing child playbooks.

   [image](https://github.com/user-attachments/assets/47181ade-244f-437e-b989-999f4ee2d8e0)

-  Move common.yml file into the newly created static-assignments folder.

   ![image](https://github.com/user-attachments/assets/57f093e9-0c5b-4d0b-9c9c-72912dbd2b2a)

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
![image](https://github.com/user-attachments/assets/8795675b-8959-4088-8e3d-aad1ee9d914a)

-  Run ansible-playbook command against the dev environment.

Push to your github repo and create a pull request, carefully check codes and merge into main branch.
![image](https://github.com/user-attachments/assets/338fe370-5236-491d-bc7e-aa157b37e674)

New Branch Got updated on remote repository.
![image](https://github.com/user-attachments/assets/3dfe22c6-e2ec-4fe7-ae02-bcdb2f1cc47c)

merging the code through PR (Pull Request.)

![image](https://github.com/user-attachments/assets/df6739fa-433d-4f36-ab5e-530cbb5a3b87)

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


- 

