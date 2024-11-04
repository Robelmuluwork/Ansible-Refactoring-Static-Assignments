# Reference Webserver role.

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
