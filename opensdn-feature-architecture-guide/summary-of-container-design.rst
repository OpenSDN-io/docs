.. _Understanding OpenSDN Containers:

Understanding OpenSDN Containers
================================

:date: 2019-05-23

Some subsystems of OpenSDN solution are delivered as Docker
containers.

OpenSDN Containers
------------------

The following are key features of the new architecture of OpenSDN
containers:

-  All of the OpenSDN containers are multiprocess Docker containers.

-  Each container has an INI-based configuration file that has the
   configurations for all of the applications running in that container.

-  Each container is self-contained, with minimal external orchestration
   needs.

-  A single tool, *Ansible*, is used for all levels of building,
   deploying, and provisioning the containers. The *Ansible* code for
   the OpenSDN system is named ``tf-ansible`` and kept in a
   separate repository. The *OpenSDN Ansible* code is responsible for
   all aspects of OpenSDN container build, deployment, and basic
   container orchestration.

 
