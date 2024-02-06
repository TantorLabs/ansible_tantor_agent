Ansible Role: Tantor Agent
=========

Ansible role for management lifecycle Tantor Platform Agent (install, register, delete)

Requirements
------------

No special requirements; note that this role requires root access, so either run it in a playbook with a global become: yes, or invoke the role in your playbook like:

```
- hosts: database
  roles:
    - role: tantor_agent
      become: yes
```

Role Variables
--------------
```
tantor_agent_url: https://server
```
Address Tantor Platform server

```
tantor_agent_validate_certs: true
```
Check certificate for downloaded packages

```
tantor_agent_token: some_token
```
Platform token for the registering agent

```
tantor_agent_install_mode: local
```
platform agent installation mode
local - Download deb/rpm packages from the reverse proxy (nginx) component of the platform without connecting to external resources. 
Install the agent version corresponding to the platform version. 
The tantor_agent_version parameter is ignored
repo - Install the platform agent from the Tantor Labs public repository
purge - Delete package agent from system and deregister agent from Platform
deregister - Deregister agent from Platform without delete package


```
tantor_agent_version: 3.2.0
```
the version of the agent to be installed, only applies to installing an agent from the repository

```
tantor_agent_nats_endpoint: server:4222
```
Connection address to the Nats server

**Agent settings for registration in the Tantor Platform**
```
tantor_agent_workspace_name: test
```
the name of the workspace to which the database instance is added
```
tantor_agent_environment: SERVER
```
environment where the DBMS is running (SERVER, CONTAINER)
```
tantor_agent_db_type: PostgreSQL
```
Database type managed by the platform agent (PostgreSQL, TantorDB)

```
tantor_agent_host_ip: '1.2.3.4'
```
IP address of the database that will be used to connect to the DBMS

```
tantor_agent_host_port: 5432
```
DBMS port used for metrics collection by the agent

```
tantor_agent_db_name: postgres
```
Name of the database through which DBMS statistics and metrics are collected

```
tantor_agent_db_password: pma_user
```
database connection password

```
tantor_agent_db_user: pma_user
```
the user under whom the DBMS connection will be made

```
tantor_agent_managed_patroni: false
```
this DBMS is managed by Patroni
```
tantor_agent_patroni_ssl: false
```
type of connection to Patroni API

```
tantor_agent_patroni_host: '1.2.3.4'
```
connection ip address to Patroni API. The default address is used from the variable tantor_agent_host_ip
```
tantor_agent_patroni_port: 8008

```
connection port to Patroni API

```
tantor_agent_patroni_user: test_user
```
authentication parameters to Patroni API

```
tantor_agent_patroni_password: test_pass
```
authentication parameters to Patroni API



Dependencies
------------

No dependencies

Example Playbook
----------------

**installation on a database managed by Patroni**

```
    - hosts: databases
      become: true
      roles:
         - { role: tantor_agent }
```
Inside group_vars/databases.yml:
```
tantor_agent_url: https://tantorlabs.ru
tantor_agent_token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJwbWEiLCJleHAiOjE3MDM5NzAwMDAsImlhdCI6MTY5NTk5NTM5Niwic2NvcGUiOiJJTlNUQUxMIn0.BM0kXMJ1BEH4w7huyXqwgIeCPcWSAlmilhsMKpvyMMM
tantor_agent_nats_endpoint: tantorlabs.ru:4222
tantor_agent_install_mode: repo
```
Inside host_vars/pg01.yml:
```
tantor_agent_host_ip: "192.168.0.1"
tantor_agent_workspace_name: test
tantor_agent_environment: SERVER
tantor_agent_db_type: PostgreSQL
tantor_agent_host_port: 5432
tantor_agent_db_name: postgres
tantor_agent_managed_patroni: true
tantor_agent_patroni_ssl: false
tantor_agent_patroni_user: username
tantor_agent_patroni_password: password
```

**installation on a database without Patroni**

```
    - hosts: databases
      roles:
         - { role: tantor_agent }
```
Inside group_vars/databases.yml:

```
tantor_agent_url: https://tantorlabs.ru
tantor_agent_token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJwbWEiLCJleHAiOjE3MDM5NzAwMDAsImlhdCI6MTY5NTk5NTM5Niwic2NvcGUiOiJJTlNUQUxMIn0.BM0kXMJ1BEH4w7huyXqwgIeCPcWSAlmilhsMKpvyMMM
tantor_agent_nats_endpoint: tantorlabs.ru:4222
tantor_agent_install_mode: repo
```
Inside host_vars/pg01.yml:
```
tantor_agent_host_ip: "192.168.0.1"
tantor_agent_workspace_name: test
tantor_agent_environment: SERVER
tantor_agent_db_type: PostgreSQL
tantor_agent_host_port: 5432
tantor_agent_db_name: postgres
```

License
-------

BSD

Author Information
------------------

This role was created in 2023 by Tantor Labs company
