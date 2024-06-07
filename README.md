## Ansible role: Tantor Agent

## General Information

This repository serves the purpose of managing the lifecycle of agent groups (install, update, uninstall).

## Repository structure

```
|-- agent.yml                             # A playbook that runs the tantor_agent role
|-- group_vars                            # A directory containing a set of variables for node groups
|   |-- all.yml                           # Set of variables applied on the whole group of nodes
|-- inventory                             # The inventory file that needs to be filled in for ansible to work
|-- inventory_template                    # An inventory file template with variable descriptions in English
|-- LICENSE                               # Description of the license under which this playbook is distributed
|-- README.md                             # Description of the repository in English
|-- README_rus.md                         # Description of the repository in Russian
|-- roles                                 # The directory containing the roles executed by ansible
|   |-- tantor_agent                      # A tantor_agent role that is installed on all nodes specified in the playbook
|   |   |-- defaults                      # Directory containing the default values for the tantor_agent role
|   |   |   | -- main.yml                 # The main file of the Defaults directory
|   |   |-- files                         # Directory containing additional files used during ansible operation
|   |   |   | -- id_rsa.pub               # The public key that will be placed in the authorized_keys of the postgres user
|   |   |-- handlers                      # Catalog containing playbook event handlers
|   |   |   |-- main.yml                  # The main file of the handlers directory
|   |   |-- meta                          # Каталог, содержащий информацию о роли
|   |   |   |-- main.yml                  # A catalog containing information about the role
|   |   |-- tasks                         # A directory containing the main tasks performed by the tantor_agent role on the group of nodes specified in the playbook
|   |   |   |-- agent_deregister.yml      # File containing tasks to deregister the agent
|   |   |   |-- agent_registration.yml    # File containing tasks for agent registration
|   |   |   |-- delete_agent.yml          # File containing tasks to remove the agent
|   |   |   |-- install_agent_local.yml   # File containing tasks to install the agent without consulting external repositories
|   |   |   |-- install_agent_repo.yml    # File containing tasks to install an agent from the Tantor external repository
|   |   |   |-- main.yml                  # The main file of the tasks directory
|   |   |   |-- prepare_nodes.yml         # File containing tasks to prepare assemblies for further operation
|   |   |-- tests                         # A catalog containing instructions for the role test
|   |   |   |-- inventory                 # Inventory file for use during testing of the tantor_agent role
|   |   |   |-- test.yml                  # Playbook for testing the role of tantor_agent
```

## Requirements

On all nodes specified in the inventory file, the following components should be installed:
* Python3 (with pip module) >= 3.10.0;
* psycopg2 >= 2.5.1 (it's recommended to install via pip)

On the control node (node where ansible-playbook command is going to be launched) the following components should be installed:
* [Ansible](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html) >= 9.5.0 (core version 2.16);

The playbook runs under a useraccount that has passwordless access to all nodes of the file inventory with the ability to switch to privileged mode (root) without entering a password.

## Preparation steps

Make sure that there is a Workspace in Tantor Platform to which agents will be added (playbook variable ``tantor_platform_workspace_name`` of ``group_vars`` ) and a token of Install type (playbook variable ``platform_workspace_name`` of group_vars ). In case of absence of the required Workspace or token - create new ones.

In the process of prepping the playbook for launch, all files to be looked at can be identified, using the symbols ``# ! #``. To do this, while in the directory of the downloaded project, execute:

```bash
grep -r --exclude='*README*' '# ! #' ./* 
./agent.yml: hosts: sample_group # ! # Change sample_group to appropriate group from inventory file
./group_vars/all.yml:# ! ! #
./host_vars/hostnameX.yml:# ! ! #
./inventory_template:sample_group # ! ! # This template should be replaced with real group of hosts; When a new group is added (in section below) - it should be also added here;
./inventory_template:[sample_group] # ! ! # Template group name
./roles/tantor_agent/files/id_rsa.pub:# ! ! # 
```

Fill in the files according to the instructions below:

1. Using the example ``inventory_template`` file in the root directory of the playbook, create your own ``inventory``. Pay attention to the comments in the source file;
2. Using the ``group_vars/all.yml`` file as an example, create and place a new file in the ``group_vars`` directory containing the variables for the node group from the ``inventory`` file (e.g., the ``sample_group.yml`` file for the ``sample_group`` group from ``inventory_template``);
3. Fill the file created in step 2 above with the data relevant to the playbook launch in the required outline;
4. If necessary, write the public key of the user, on behalf of whom the Playbook is launched, in the file ``roles/tantor_agent/files/id_rsa.pub``. This key will be added to the ``postgres`` user;
5. If it is necessary to override variables at the node level, add a file with the name of this node and the extension ``.yml`` to the ``host_vars`` directory (for example, the file ``hostnameA.yml`` from ``inventory_template``).
6. Replace the ``sample_group`` value from the ``agent.yml`` file with the group specified in the ``inventory`` file for which all tasks will be executed;

## Playbook launch

The main variable file used by the playbook is ``group_vars/<file created in step 2 of the instructions above>.yml``. It is where the basic logic of the playbook is configured.

---
To start the playbook use the command:
```bash
ansible-playbook -i <path to inventory file> -l <group of nodes from inventory file> -D
```
