.. _integrating-tf-release-50x-with-vmware-vrealize-orchestrator:

Integrating OpenSDN Release 5.0.X with VMware vRealize Orchestrator
===========================================================================

:date: 2019-07-29

A dedicated OpenSDN plugin is used to connect to VMware vRealize
Orchestrator (vRO). OpenSDN 5.0 supported a Beta version of the
plugin. Starting with OpenSDN 5.0.1, a fully supported version
of the plugin is available. The plugin is used to view the OpenSDN
controller configuration in the vRO inventory, and to modify
configurations by using vRO workflows. You can also create network
policies, security groups, and automate both simple and complex
workflows by using vRO.

Components of vRealize Orchestrator
-----------------------------------

vRO consists of the following components:

-  vRO Inventory—The vRO inventory displays the OpenSDN plugin and the
   OpenSDN node or endpoint. All OpenSDN plugin objects that represent
   your system are displayed in the vRO Inventory. Objects are displayed
   in a hierarchical order and are based on the OpenSDN schema.

   With Release 5.0, OpenSDN inventory objects such as projects,
   security groups, virtual networks, network IPAMs, network policies,
   ports, floating IP pools, and service templates are displayed in the
   vRO inventory. Relevant API objects are also displayed in the vRO
   inventory.

-  vRO Workflows—The vRO workflow is a process that manipulates objects
   in a vRO Inventory. Each plugin has a set of predefined workflows.
   vRO combines workflows from different plugins to create complex
   processes and manages them. Multiple workflows are used to create
   blueprints in vRA.

   .. note::

      VMware vCenter plugin workflows are represented as simple workflows
      in vRO plugin.

OpenSDN workflows
-------------------------

You must connect to the OpenSDN controller or an endpoint before you
create OpenSDN workflows. You must use **Create OpenSDN controller
connection** to connect to an endpoint. You must use **Delete OpenSDN
controller connection** to terminate a connection with an endpoint. Once
you connect to the Control controller, the vRO plugin accesses the
OpenSDN inventory objects and then updates the vRO inventory with the
OpenSDN inventory objects.

The following workflows are defined for simple networking operations in
OpenSDN 5.0:

-  Network

   -  Create network

   -  Delete network

   .. note::

      You must use the **Create network** workflow to create a network on
      the OpenSDN controller.

-  Network Policy

   -  Create network policy

   -  Add rule to network policy

   -  Remove network policy rule

   -  Delete network policy

-  Security policy

   -  Create security group

   -  Add rule to security group

   -  Edit security group

   -  Remove security group rule

   -  Delete security group

-  Service Instance

   -  Add port tuple to service instance

   -  Create service instance

   -  Delete service instance

   -  Remove port tuple from service instance

-  Network IPAM

-  Port

-  Project

-  Service Template

-  Virtual Network

-  Floating IP

   -  Create floating IP

   -  Delete floating IP

-  Floating IP pools

   -  Create floating IP pool

   -  Delete floating IP pool

   -  Edit floating IP pool

Starting with OpenSDN 5.0.1, the following workflows are also
defined:

-  Tag

   -  Create global tag

   -  Create tag in project

   -  Delete tag

-  Tag Type

   -  Create tag type

   -  Delete tag type

-  Network Policy

   -  Edit network policy rule

-  Security policy

   -  Edit security group rule

-  Service Health Check

   -  Create service health check

   -  Add service instance to service health check

   -  Remove service instance from service health check

   -  Edit service health check

   -  Delete service health check

-  Project

   -  Add application policy set to project

   -  Remove application policy set to project

   -  Add tag to project

   -  Remove tag from project

-  Virtual Network

   -  Add tag to virtual network

   -  Remove tag from virtual network

-  Virtual Machine Interface (VMI) - Port

   -  Add tag to port

   -  Remove tag from port

-  Service Group

   -  Create service group in policy management

   -  Create service group in project

   -  Add service to service group

   -  Edit service of service group

   -  Remove service from service group

   -  Delete service group

-  Address Group

   -  Create global address group

   -  Create address group in project

   -  Add subnet to address group

   -  Remove subnet from address group

   -  Delete address group

   -  Add label to address group

   -  Remove label from address group

-  Application Policy Set

   -  Create global application policy set

   -  Create application policy set in project

   -  Add firewall policy to application policy set

   -  Remove firewall policy from application policy set

   -  Add tag to application policy set

   -  Remove tag from application policy set

   -  Delete application policy set

-  Firewall Policy

   -  Create global firewall policy

   -  Create firewall policy in project

   -  Add firewall rule to firewall policy

   -  Remove firewall rule from firewall policy

   -  Delete firewall policy

-  Firewall Rule

   -  Create global firewall rule

   -  Create firewall rule in project

   -  Edit firewall rule

   -  Delete firewall rule

 
