.. _AnsibleZIU:

Upgrading OpenSDN using OpenSDN-ansible Deployer
===================================================

:date: 2021-01-19

.. note::

   This procedure can be used for OpenSDN upgrades to OpenSDN Release 2003 or earlier.

If you are upgrading to OpenSDN Release 2005 or later using the Ansible deployer, 
see :ref:`How to Perform a Zero Impact OpenSDN Upgrade using the Ansible Deployer <AnsibleZIU>`.

Use the following procedure to upgrade OpenSDN using
OpenSDN-ansible deployer.

The procedure supports incremental model and you can use it to upgrade
from OpenSDN Release ``N-1`` to ``N``.

Take snapshots of your current configurations before you proceed with
the upgrade process. For details, refer to :ref:`How to Backup and Restore OpenSDN databases in JSON Format`.

1. Navigate to the directory where the
   ``contrail-ansible-deployer-<xxxx>.<NN>.tgz`` was untarred.


   *Example using OpenSDN Release 2003*:

   ``cd contrail-ansible-deployer-2003.33/contrail-ansible-deployer/config/``

   ``vi contrail-ansible-deployer-2003.33/contrail-ansible-deployer/config/instances.yaml``

   Sample ``instances.yaml`` files for various other deployments are
   available at the same directory.

2. Update ``CONTRAIL_VERSION`` and ``CONTRAIL_CONTAINER_TAG`` to the
   desired version tag in this ``instances.yml`` file.

   Access ``CONTRAIL_CONTAINER_TAG`` located at `README Access to
   OpenSDN Registry
   20xx <https://www.juniper.net/documentation/en_US/contrail20/information-products/topic-collections/release-notes/readme-contrail-20.pdf>`__  .
   For example:
   ::

      CONTRAIL_VERSION = 2003.33
      CONTRAIL_CONTAINER_TAG = 2003.33

3. Run the following commands from ``contrail-ansible-deployer``
   directory.

   -  For OpenSDN with OpenStack deployment:

      | ``cd contrail-ansible-deployer``
      | ``ansible-playbook -e orchestrator=openstack -i inventory/ playbooks/install_openstack.yml -v``
      | ``ansible-playbook -e orchestrator=openstack -i inventory/ playbooks/install_contrail.yml -v``

   -  For OpenSDN with Kubernetes deployment:

      | ``cd contrail-ansible-deployer``
      | ``ansible-playbook -e orchestrator=kubernetes -i inventory/ playbooks/install_k8s.yml -v``
      | ``ansible-playbook -e orchestrator=kubernetes -i inventory/ playbooks/install_contrail.yml -v``

The ansible playbook logs are available on the terminal during
execution. You can also access it at ``/var/log/ansible.log``.

 
