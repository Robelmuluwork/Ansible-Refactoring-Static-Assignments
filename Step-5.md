## Step 5: Commit & Test

- Commit your changes to your Git repository.
- Create a Pull Request and merge it into the main branch.

![image](https://github.com/user-attachments/assets/64eec96b-1e3a-4b13-a06e-5aa599bf757b)

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
