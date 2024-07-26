Understanding OpenSDN quotas
========================================

:date: 2024-07-25

Overview: OpenSDN quotas
----------------------------

Quotas are used to prevent system capacities from being exhausted without notification. Quotas are operational limits.

Quotas are currently enforced at the tenant (or project) level. OpenSDN support quotas for the next resources:

- Loadbalancer pools

- Subnets

- Loadbalancer listeners

- Networks

- Network IPAMs

- Routers

- Security Group Rules

- Service Instances

- Floating IP's

- Floating IP pools

- Loadbalancer Members

- Ports

- Policies

- Loadbalancer Health monitors

- Virtual IP's

- Security Groups

- Loadbalancers

Quota could be set on a project with WebUI and through the VNC API.

Using OpenSDN quotas with WebUI
-------------------------------

WebUI uses a VNC API to set and get project quota settings. To get a current quota usage/limits WebUI counting list of resource

objects for each resource type what could be slow with a large amount of resources.

To get and configure quotas open Configure -> Infrastructure -> Project Settings -> Quotas (opened by default)

Using OpenSDN quotas with Openstack (tf-neutron-plugin)
--------------------------------------------------------

Openstack (tf-neutron-plugin) uses a VNC API to set and get project quota settings. To get a current quota usage/limits

tf-neutron-plugin counting list of resource objects for each resource what could be slow with a large amount of resources.

+----------------------------------+-------------------------------------------------------------------------+
| Action                           | Command                                                                 |
+==================================+=========================================================================+
| List default project quotas      | ``openstack quota show --default  <proj_uuid>``                         |
+----------------------------------+-------------------------------------------------------------------------+
| List quota and quota usage       | ``openstack quota list --project <uuid>  --network  --detail``          |
+----------------------------------+-------------------------------------------------------------------------+
| Set project quota                | ``openstack quota set <uuid> --<type>=<value>``                         |
+----------------------------------+-------------------------------------------------------------------------+


Creating resources with quota limits
------------------------------------

For each resource per a project, OpenSDN stores quota counter in Zookeeper. When user tries to create a new resource with

applied limits current quota usage will be verified and 412 error will be raised in case of OverQuota. New resource will be

created if quota is verified. Quota rolled back if resource creation failed with any other reasons.


Check current quotas in Zookeeper
---------------------------------

The following command could help to check quotas in Zookeeper.

+--------------------------------------------+-------------------------------------------------------------------------+
| Action                                     | Command                                                                 |
+==================================+===================================================================================+
| Connect to ZK                              | ``bin/zkCli.sh -server <zk_ip>:<zk_port|2181>``                         |
+--------------------------------------------+-------------------------------------------------------------------------+
| Get quota usage (counter                   | ``get /vnc_api_server_obj_create/<proj_uuid>/<reource_name>``           |
+--------------------------------------------+-------------------------------------------------------------------------+
| List resources with a quota counter        | ``ls /vnc_api_server_obj_create/<proj_uuid>``                           |
+--------------------------------------------+-------------------------------------------------------------------------+

