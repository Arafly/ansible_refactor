## Ansible Refactoring & Static Assignments (Imports and Roles)

In this project we'll continue working with the *automate-everything* repository and make some improvements of our code.
Here, we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows us to organize our tasks and reuse them when needed.

### Jenkins Job Amelioration
First, let's make some changes to our Jenkins job - before now, every new change in the codes creates a separate directory, which truthfully, is not very convenient when we want to run some commands from one place. Besides, it saps the space on the Jenkins server with every subsequent change. Let's ameliorate this, by introducing a new Jenkins project/job, we'll also require `Copy Artifact plugin`.

- Proceed to your Jenkins-Ansible server and create a new directory called 'ansible-artifact' - this is where we'll store all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-artifact`

- Change permissions to this directory, so Jenkins could save files there 

`chmod -R 0777 /home/ubuntu/ansible-artifact`

- Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins
- Create a new Freestyle projecct and name it 'save_artifacts' - This project will be triggered by completion of your existing ansible project. 
Configure it accordingly:

![](https://github.com/Arafly/ansible_refactor/blob/master/assets/build_retention.png)

> The goal of the "save_artifacts" project is to save artifacts into `/home/ubuntu/ansible-artifact` directory. In order to achieve this:
- we need to create a Build step and choose Copy artifacts from other project, specify "ansible as a source project" and /home/ubuntu/ansible-artifact as a target directory.

![](https://github.com/Arafly/ansible_refactor/blob/master/assets/copy_artifact_build.png)

- Test your set up by making some change in README.MD file inside your *automate-everything *repository (right inside master branch).

If both Jenkins jobs have completed one after another - you shall see your files inside /home/ubuntu/ansible-artifact directory and it will be updated with every commit to your master branch.

![](https://github.com/Arafly/ansible_refactor/blob/master/assets/upstream.png)

![](https://github.com/Arafly/ansible_refactor/blob/master/assets/downstream.png)

![](https://github.com/Arafly/ansible_refactor/blob/master/assets/downstream_deets.png)


## Refactor Ansible code by importing other playbooks into site.yml



