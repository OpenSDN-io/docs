2. OpenSDN + Devstack setup(Ansible Deployer)
-----------------------------------------------------

Install Centos 7 for a stable installation of OpenSDN.

You can also use Ubuntu 20.04 (In case of problems, check https://github.com/OpenSDN-io/tf-devstack)

Ref: https://github.com/OpenSDN-io/tf-devstack

If you have any questions, you can see more detailed instructions in this README:

https://github.com/OpenSDN-io/tf-devstack/tree/master/ansible

2.1 Create a nonroot user and add it to the wheel group
-------------------------------------------------------

::

         sudo useradd <username>
         sudo passwd <username> 
         sudo usermod -aG wheel <username>


Modify user rigths configuration. Open the sudoers file

::

         sudo visudo

In the opened file, locate the line

::

         %wheel  ALL=(ALL)       ALL

Change it to 

::

         %wheel ALL=(ALL)        NOPASSWD: ALL

Switch to the new user and navigate to the home directory

::

         su <username>
         cd


2.1 Clone Contrail-installer and devstack Repositories
------------------------------------------------------

::

        git clone http://github.com/OpenSDN-io/tf-devstack
   

2.2 Set up OpenSDN
--------------------------

Execute script and wait for installation:
::

        ./tf-devstack/ansible/run.sh  // 

2.3 Accessing Openstack and OpenSDN GUIs
------------------------------------------------
Openstack and contrail GUIs can be accessed as follows:-

1. OpenSDN GUI: :code:`http://localhost:8143/`

2. Openstack GUI: :code:`http://localhost/dashboard/` (only if you have installed with the openstack orchestrator)