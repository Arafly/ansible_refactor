## Ansible Refactoring & Static Assignments (Imports and Roles)

In this project we'll continue working with the *automate-everything* repository and make some improvements of our code.
Here, we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows us to organize our tasks and reuse them when needed.

### Jenkins Job Amelioration
First, let's make some changes to our Jenkins job - before now, every new change in the codes creates a separate directory, which truthfully, is not very convenient when we want to run some commands from one place. Besides, it saps the space on the Jenkins server with every subsequent change. Let's ameliorate this, by introducing a new Jenkins project/job, we'll also require `Copy Artifact plugin`.

- Proceed to your Jenkins-Ansible server and create a new directory called 'ansible-artifact' - this is where we'll store all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-artifact`

- Change permissions to this directory, so Jenkins could save files there 

`chmod -R 0777 /home/ubuntu/ansible-config-artifact`

- Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins
- Create a new Freestyle projecct and name it 'save_artifacts' - This project will be triggered by completion of your existing ansible project. 
Configure it accordingly:

