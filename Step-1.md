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


