`Table of Contents <#table-of-contents>`__
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  `Overview <#overview>`__
-  `Setup <#setup>`__
-  `Traffic Control Aliases <#traffic-control-aliases>`__
-  `Traffic Control Environments <#traffic-control-environments>`__

   -  `TC\_ENVS <#tc_envs>`__

-  `General Command Cheat Sheet
   FAQ <#general-command-cheat-sheet-faq>`__
-  `Yum Repository <#yum-repository>`__
-  `Playbook Commandline Variables <#playbook-commandline-variables>`__
-  `Executing playbook on single
   host <#executing-playbook-on-single-host>`__
-  `Executing playbook on all
   hosts <#executing-playbook-on-all-hosts>`__

Overview
^^^^^^^^

Traffic Control uses Ansible for automating our environment setup,
deploying, or just general tooling. It assumes a basic understanding of
what Ansible is and how to use it (See setup links below)

Setup
^^^^^

-  `Install
   Ansible <http://docs.ansible.com/ansible/intro_installation.html>`__
-  `Ansible
   Documentation <http://docs.ansible.com/ansible/index.html>`__
-  Setup TC\_ENVS to point to the local 'environments' (or external
   directory that is source controlled). To make the environment
   variable permanent add it to the .profile for your shell.

::

    $ export TC_ENVS="full path to your 'ansible_automation/environments' directory."

Traffic Control Component Aliases
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Traffic Control has the following components so when you see variables
in the playbooks like **to\_user** that means a 'Traffic Ops' user

+-------------------+------------+
| Component         | Acronym    |
+===================+============+
| Ansible           | ansible    |
+-------------------+------------+
| Influxdb          | influxdb   |
+-------------------+------------+
| Traffic Monitor   | tm         |
+-------------------+------------+
| Traffic Ops       | to         |
+-------------------+------------+
| Traffic Portal    | tp         |
+-------------------+------------+
| Traffic Router    | tr         |
+-------------------+------------+
| Traffic Stats     | ts         |
+-------------------+------------+
| Postgres          | pg         |
+-------------------+------------+
| Riak              | riak       |
+-------------------+------------+

Traffic Control Ansible Directory Structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Directory structure:

::

    ansible_automation
    ├── environments
    │   └── dev
    ├── roles
    │   ├── common
    │   ├── confighost
    │   ├── databases
    │   ├── languages
    │   ├── traffic_analytics
    │   │   ├── elasticsearch
    │   │   │   ...
    │   │   ├── heka
    │   │   │   ...
    │   │   ├── kafka
    │   │   │   ...
    │   │   ├── kibana
    │   │   │   ...
    │   │   ├── telegraf
    │   │   │   ...
    │   │   └── zookeeper
    │   │   │   ...
    │   ├── traffic_control
    │   │   ├── traffic_logs_generator
    │   │   │     ...
    │   │   ├── traffic_monitor
    │   │   │     ...
    │   │   ├── traffic_ops
    │   │   │     ...
    │   │   └── traffic_router
    │   │   │     ...
    │   ├── updateserver
    │   │   │     ...
    │   ├── users
    │   │   │     ...
    │   └── yumrepos
    │   │   │     ...

Directory Structure Meaning
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- common
- confighost
- databases
- languages
- traffic\_analytics/elasticsearch
- traffic\_analytics/heka
- traffic\_analytics/kafka
- traffic\_analytics/kibana
- traffic\_analytics/telegraf
- traffic\_analytics/zookeeper
- traffic\_control/traffic\_logs\_generator
- traffic\_control/traffic\_monitor
- traffic\_control/traffic\_ops
- traffic\_control/traffic\_router

- users

- yumrepos

Playbook Environments
^^^^^^^^^^^^^^^^^^^^^

The Traffic Control playbooks are built in such a way that a user can
define an "environment", so that static variables can be kept in source
control so that the next person can use them as well. The environment
variable \*\*\*\*TC\_ENVS\*\*\*\* is referenced in the playbooks such
that if specified will tell the playbooks to look in a "side" directory
for any playbook varible overrides.

Playbook 'docker' tags
^^^^^^^^^^^^^^^^^^^^^^

TC\_ENVS
''''''''

General Command Cheat Sheet FAQ
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

--lists-hosts --limit examples

| Q: How can I automate setting up users on all of my Traffic Control
  hosts?
| A: If your users are in Traffic Ops (whose roles are 'admin' and have
  their "Publish SSH Key" populated), the 'setup\_host.yml' playbook can
  extract the users' ssh keys from Traffic Ops and create remote users
  on your hosts accordingly. The only requirement is an existing user
  must existing to bootstrap the process. In this example 'poweruser'
  has sudo access and the host where the command is being executed has
  the 'poweruser' private ssh key available.

See the following example command:

    ``$ ansible-playbook setup_host.yml -i 127.0.0.1, --extra-vars "env=test remote_user=poweruser``
    ``to_username=your-to-user to_password=your-to-password" --private-key=~/.ssh/poweruser_id_rsa``

Yum Repository
^^^^^^^^^^^^^^

For example, see below:

--------------

yumrepos: - global: name: your\_repo baseurl:
"http://your\_repo\_url/repo" descr: "My Custom Yum Repo"

Playbook Commandline Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------+---------------------------+
| Extra Vars         | Example Value             |
+====================+===========================+
| **remote\_user**   | some-user                 |
+--------------------+---------------------------+
| **to\_url**        | http://traffic\_ops.com   |
+--------------------+---------------------------+

Executing playbook on single host
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

    ``$ ansible-playbook -i inventory/inventory --limit 127.0.0.1 --extra-vars "remote_user=some-user to_url=http://``
    ``traffic_ops.com``

Executing playbook on all hosts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``ansible-playbook -i inventory/inventory --extra-vars "remote_user=some-user to_url=http://traffic_ops.com" *.yml``
