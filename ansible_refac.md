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

Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it *refactor*.

In Project 11 we wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine we have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and the playbook will become messy with many commented parts. 

Let see code re-use in action by importing other playbooks.

Within playbooks folder, create a new file and name it site.yml - This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, `site.yml` - will become a parent to all other playbooks that will be developed. Including common.yml that you created previously.

- Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work.

- Move *common.yml* file into the newly created static-assignments folder.

- Inside site.yml file, import common.yml playbook.

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

> The code above uses built in import_playbook Ansible module.

Your folder structure should look like this;

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

- Run ansible-playbook command against the dev environment
Since you need to apply some tasks to your dev servers and wireshark is already installed - you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

```
---
- name: update web, nfs and db servers
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

- update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

`sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml`

Make sure that wireshark is deleted on all the servers by running wireshark --version

### Configure UAT Webservers with a role ‘Webserver’



