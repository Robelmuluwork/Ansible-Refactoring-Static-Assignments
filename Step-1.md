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

![image](https://github.com/user-attachments/assets/e9af9ac7-a5a6-4ecc-94d9-b1474a915d4d)

4. Create a new Freestyle project and name it save_artifacts.

![image](https://github.com/user-attachments/assets/07208141-a05c-46ac-a242-8702fad5f2bb)

5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:

![image](https://github.com/user-attachments/assets/b955b451-65b1-4f7b-b780-6fa934de2972)
note: We can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

![image](https://github.com/user-attachments/assets/9199de56-2722-45ab-8724-70b61d1929f0)

![image](https://github.com/user-attachments/assets/2f191d65-50da-4f21-83b4-ed8b8767a017)

![image](https://github.com/user-attachments/assets/8cd28a65-344d-4787-8b3f-25565fbd4f11)


7. Test set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside main branch).

Doing changes in ansible-config-mgt it will goint to trigger the ansible jobs due to Webhook which we Already setup.

![image](https://github.com/user-attachments/assets/ab8ab539-5ae2-4fe2-85d7-5959294dbdfa)

It will goint to trigger ansible once its get success it will going to trigger the save_artifacts.

We can artifact copied to ansible-config-artifact/ directory.
![image](https://github.com/user-attachments/assets/c763f0f3-829e-413a-aef9-423b714ad82e)

