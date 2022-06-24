Updating Tungsten Fabric using the Zero Impact Upgrade Process in an Environment using Red Hat Openstack
============================================================================================================

:date: 2020-09-16 

This document provides the steps needed to update a Tungsten Fabric
deployment that is using Red Hat Openstack as it’s orchestration
platform. The procedure provides a zero impact upgrade (ZIU) with
minimal disruption to network operations.

When to Use This Procedure
--------------------------

This procedure is used to upgrade Tungsten Fabric when it is running
in environments using RHOSP13.

The procedure in this document has been validated for the following
Tungsten Fabric upgrade scenarios:

Table 1: Tungsten Fabric with RHOSP13 Validated Upgrade Scenarios

+----------------------------------+----------------------------------+
| Starting Tungsten Fabric         | Target Upgraded Tungsten Fabric  |
| Release                          | Networking Release               |
+==================================+==================================+
| 1912.L0                          | 1912.L1                          |
+----------------------------------+----------------------------------+
| 1912.L0                          | 2003                             |
+----------------------------------+----------------------------------+
| 2003                             | 2005                             |
+----------------------------------+----------------------------------+
| 2005                             | 2008                             |
+----------------------------------+----------------------------------+

A different procedure is followed for upgrading to earlier target
Tungsten Fabric releases in environments using RHOSP13
orchestration. See `Upgrading Tungsten Fabric with Red Hat Openstack
13 using ISSU <rhosp13-issu>`_.


If you want to use this procedure to upgrade your Tungsten Fabric
release to other releases, you must engage Juniper Networks professional
services. Contact your Juniper representative for additional
information.

Prerequisites
-------------

This document makes the following assumptions about your environment:

-  A Tungsten Fabric deployment using Red Hat Openstack version 13
   (RHOSP13) as the orchestration platform is already operational.

-  The overcloud nodes in the RHOSP13 environment have an enabled Red
   Hat Enterprise Linux (RHEL) subscription.

-  Your environment is running TF Release 1912 and upgrading to
   TF Release 1912-L1 or to TF Release 2003 or later.

-  If you are updating Red Hat Openstack simultaneously with Tungsten Fabric, we assume that the undercloud node is updated to the
   latest minor version and that new overcloud images are prepared for
   an upgrade if needed for the upgrade. See the `Upgrading the
   Undercloud <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/keeping_red_hat_openstack_platform_updated/assembly-upgrading_the_undercloud>`__
   section of the `Keeping Red Hat OpenStack Platform
   Updated <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/keeping_red_hat_openstack_platform_updated/index>`__
   guide from Red Hat.

   If the undercloud has been updated and a copy of the heat templates
   are used for the deployment, update the copy of the heat template
   from the Red Hat’s core heat template collection at
   /usr/share/openstack-tripleo-heat-templates. See the `Understanding
   Heat
   Templates <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/advanced_overcloud_customization/sect-understanding_heat_templates>`__
   document from Red Hat for information on this process.

Before You Begin
----------------

We recommend performing these procedures before starting the update:

-  Backup your TF configuration database before starting this
   procedure. See :ref:`How to Backup and Restore TF databases in JSON Format`.

-  Each compute node agent will go down during this procedure, causing
   some compute node downtime. The estimated downtime for a compute node
   varies by environment, but typically took between 12 and 15 minutes
   in our testing environments.

   If you have compute nodes with workloads that cannot tolerate this
   downtime, consider migrating workloads or taking other steps to
   accommodate this downtime in your environment.

-  If you are updating Red Hat Openstack simultaneously with Tungsten Fabric, 
   update Red Hat Openstack to the latest minor release
   version and ensure that the new overcloud images are prepared for the
   upgrade. See the `Upgrading the
   Undercloud <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/keeping_red_hat_openstack_platform_updated/assembly-upgrading_the_undercloud>`__
   section of the `Keeping Red Hat OpenStack Platform
   Updated <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/keeping_red_hat_openstack_platform_updated/index>`__
   guide from Red Hat for additional information.

   If the undercloud has been updated and a copy of the heat templates
   are used for the deployment, update the Heat templates from Red Hat’s
   core Heat template collection at
   /usr/share/openstack-tripleo-heat-templates. See the `Understanding
   Heat
   Templates <https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/advanced_overcloud_customization/sect-understanding_heat_templates>`__
   document from Red Hat for additional information.

Updating Tungsten Fabric in an Environment using Red Hat Openstack
----------------------------------------------------------------------

To update Tungsten Fabric in an environment that is using Red Hat
Openstack as the orchestration platform:

1.  Prepare your docker registry. The registry is often included in the
    undercloud, but it can also be a separate node.

    Docker registry setup is environment independent. See `Docker
    Registry <https://docs.docker.com/registry/>`__ from Docker for
    additional information on Docker registry setup.

2.  Backup the TF TripleO Heat Templates. See `Using the TF
    Heat
    Template <https://www.juniper.net/documentation/en_US/contrail20/topics/task/configuration/heat-template-vnc.html>`__.

3.  Get the TF TripleO Heat Templates (Stable/Queens branch) from
    https://github.com/Juniper/contrail-tripleo-heat-templates.

4.  (Optional) Update the TF TripleO Puppet module to the latest
    version and prepare Swift Artifacts, as applicable.

    Below are sample commands entered in the undercloud:

    ::

       [stack@queensa ~]$ mkdir -p ~/usr/share/openstack-puppet/modules/tripleo
       [stack@queensa ~]$ git clone -b stable/queens https://github.com/Juniper/contrail-tripleo-puppet usr/share/openstack-puppet/modules/tripleo
       [stack@queensa ~]$ tar czvf puppet-modules.tgz usr/
       [stack@queensa ~]$ upload-swift-artifacts -c contrail-artifacts -f puppet-modules.tgz

5.  Update the parameter ``ContrailImageTag`` to the new version.

    The location of the ``ContrailImageTag`` variable varies by
    environment. In the most commonly-used environments, this variable
    is set in the ``contrail-services.yaml`` file.

    You can obtain the ``ContrailImageTag`` parameter from the 
    :ref:`Getting Started with Tungsten Fabric <GettingStarted>`.

6.  (Recommended) If you are upgrading to Tungsten Fabric Release
    2005 or later, check and, if needed, enable kernel vRouter huge page
    support to support future compute node upgrades without rebooting.

    You can enable or verify kernel-mode vRouter huge page support in
    the contrail-services.yaml file using either the
    ContrailVrouterHugepages1GB: and ContrailVrouterHugepages2MB:
    parameters:

    ::

       parameter_defaults:
         …
         ContrailVrouterHugepages1GB: ‘2’

    ::

       parameter_defaults:
         …
        # ContrailVrouterHugepages2MB: ‘1024’

    Notes about kernel-mode vRouter huge page support in Red Hat
    Openstack environments:

    -  Kernel-mode vRouter huge page support was introduced in TF Release 2005, and is configured to support 2 1GB huge
       pages by default in Tungsten Fabric Release 2005 or later.

       A compute node has to be rebooted once for a huge page
       configuration to finalize. After this initial reboot, the compute
       node can perform future Tungsten Fabric software upgrades
       without rebooting.

       Notably, a compute node in an environment running Tungsten Fabric 
       2005 or later has not enabled huge page support for
       kernel-mode vRouters until it is rebooted. The 2x1GB huge page
       support configuration is present by default, but it isn’t enabled
       until the compute node is rebooted.

    -  We recommend only using 1GB or 2MB kernel-mode vRouter huge pages
       in most environments. You can, however, simultaneously enable 1GB
       or 2MB kernel-mode vRouter huge pages in Red Hat Openstack
       environments if your environment requires enablement of both huge
       page options.

    -  Changing vRouter huge page configuration settings in a Red Hat
       Openstack environment typically requires a compute node reboot.

       -  1 GB pages: Reboot required.

       -  2 MB: Reboot usually required. The reboot is sometimes avoided
          in environments where memory isn’t highly fragmented or the
          required number of pages can be easily allocated.

    -  We recommend allotting 2GB of memory—either using the default
       1024x2MB huge page size setting or the 2x1GB size setting—for
       huge pages in most environments. Some larger environments might
       require additional huge page memory settings for scale. Other
       huge page size settings should only be set by expert users in
       specialized circumstances.

    -  If the ContrailVrouterHugepages1GB: and
       ContrailVrouterHugepages2MB: parameters are set to empty value in
       the contrail-services.yaml file, vRouter huge pages are disabled.

7.  Update the overcloud by entering the openstack overcloud update
    prepare command and include the files that were updated during the
    previous steps with the overcloud update.

    Example:

    ::

       openstack overcloud update prepare 
       --templates tripleo-heat-templates/
       --roles-file tripleo-heat-templates/roles_data_contrail_aio.yaml -e
       environment-rhel-registration.yaml -e
       tripleo-heat-templates/extraconfig/pre_deploy/rhel-registration/rhel-registrationresource-registry.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-services.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-net-single.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-plugins.yaml -e
       misc_opts.yaml -e
       contrail-parameters.yaml -e
       docker_registry.yaml

8.  Prepare the overcloud nodes that include TF containers for the
    update.

    -  Pull the images in the repository onto the overcloud nodes.

       There are multiple methods for performing this step. Commonly
       used methods for performing this operation include using the
       docker pull command for Docker containers and the openstack
       overcloud container image upload command for Openstack
       containers, or running the
       tripleo-heat-templates/upload.containers.sh and
       tools/contrail/update_contrail_preparation.sh scripts.

    -  (Not required in all setups) Provide export variables for the
       script if the predefined values aren’t appropriate for your
       environment. The script location:

       ::

          ~/tripleo-heat-templates/tools/contrail/update_contrail_preparation.sh

       The following variables within the script are particularly
       significant for this upgrade:

       -  CONTRAIL_NEW_IMAGE_TAG—The image tag of the target upgrade
          version of TF. The default value is latest.

          If needed, you can obtain this parameter for a specific image
          from the :ref:`Getting Started with Tungsten Fabric Guide <GettingStarted>`

          .. note::

             Some older deployments use the CONTRAIL_IMAGE_TAG variable in
             place of the CONTRAIL_NEW_IMAGE_TAG variable. Both variables
             are recognized by the update_contrail_preparation.sh script
             and perform the same function.

       -  SSH_USER—The SSH username for logging into overcloud nodes.
          The default value is heat-admin.

       -  SSH_OPTIONS—Custom SSH option values.

          The default SSH options for your environment are typically
          pre-defined. You are typically only changing this value if you
          want to customize your update.

       -  STOP_CONTAINERS—The list of containers that must be stopped
          before the upgrade can proceed. The default value is
          contrail_config_api contrail_analytics_api.

    -  Run the script:

       .. caution::

          TF services stop working when the script starts running.

       ::

          ~/tripleo-heat-templates/tools/contrail/update_contrail_preparation.sh

9.  Update the Tungsten Fabric Controller nodes:

    -  Run the openstack overcloud update run command on the first
       TF controller and, if needed, on a Tungsten Fabric Analytics node.
       The purpose of this step is to update one Tungsten Fabric Controller and
       one Tungsten Fabric Analytics node to support the environment so the
       other Tungsten Fabric Controllers and analytics nodes can be updated
       without incurring additional downtime.

       Example:

       ::

          openstack overcloud update run --nodes overcloud-contrailcontroller-0

       Ensure that the TF status is ok on
       overcloud-contrailcontroller-0 before proceeding.

       If the analytics and the analyticsdb nodes are on separate nodes,
       you may have to update the nodes individually:

       ::

          openstack overcloud update run --nodes overcloud-contrailcontroller-0
          openstack overcloud update run --roles ContrailAnalytics,ContrailAnalyticsDatabase

    -  After the upgrade, check the docker container status and versions
       for the Tungsten Fabric Controllers and the Tungsten Fabric Analytics and
       AnalyticsDB nodes.

       ::

          docker ps -a

    -  Update the remaining Tungsten Fabric Controller nodes:

       Example:

       ::

          openstack overcloud update run --nodes overcloud-contrailcontroller-1
          openstack overcloud update run --nodes overcloud-contrailcontroller-2
          openstack overcloud update run --nodes overcloud-contrailcontroller-3
          ...

10. Update the Openstack Controllers using the openstack overcloud
    update run commands:

    Example:

    ::

       openstack overcloud update run --nodes overcloud-controller-0
       openstack overcloud update run --nodes overcloud-controller-1
       openstack overcloud update run --nodes overcloud-controller-2
       ...

11. Individually update the compute nodes.

    .. note:: 

      The compute node agent will be down during this step. The estimated
      downtime varies by environment, but is typically between 1 and 5
      minutes.

      Consider migrating workloads that can’t tolerate this downtime
      before performing this step

      ::

         openstack overcloud update run --nodes overcloud-novacompute-1
         openstack overcloud update run --nodes overcloud-novacompute-2
         openstack overcloud update run --nodes overcloud-novacompute-3
         ...

    Reboot your compute node to complete the update.

    .. note::

       A reboot is required to complete this procedure only if a kernel
       update is also needed. If you would like to avoid rebooting your
       compute node, check the log files in the /var/log/yum.log file to
       see if kernel packages were updated during the compute node update.
       A reboot is required only if kernel updates occurred as part of the
       compute node update procedure.

    ::

       sudo reboot

    Use the contrail-status command to monitor upgrade status. Ensure
    all pods reach the ``running`` state and all services reach the
    ``active`` state.

    This contrail-status command provides output after a successful
    upgrade:

    .. note::

       Some output fields and data have been removed from this
       contrail-status command sample for readability.

    ::

       Pod             Service        Original Name                     State
       analytics       api            contrail-analytics-api            running
       analytics       collector      contrail-analytics-collector      running
       analytics       nodemgr        contrail-nodemgr                  running
       analytics       provisioner    contrail-provisioner              running
       analytics       redis          contrail-external-redis           running
       analytics-alarm alarm-gen      contrail-analytics-alarm-gen      running
       analytics-alarm kafka          contrail-external-kafka           running
       analytics-alarm nodemgr        contrail-nodemgr                  running
       analytics-alarm provisioner    contrail-provisioner              running
       analytics-alarm zookeeper      contrail-external-zookeeper       running
       analytics-snmp  nodemgr        contrail-nodemgr                  running
       analytics-snmp  provisioner    contrail-provisioner              running
       analytics-snmp  snmp-collector contrail-analytics-snmp-collector running
       analytics-snmp  topology       contrail-analytics-snmp-topology  running
       config          api            contrail-controller-config-api    running
       <trimmed>

       == Contrail control ==
       control: active
       nodemgr: active
       named: active
       dns: active

       == Contrail analytics-alarm ==
       nodemgr: active
       kafka: active
       alarm-gen: active

       == Contrail database ==
       nodemgr: active
       query-engine: active
       cassandra: active

       == Contrail analytics ==
       nodemgr: active
       api: active
       collector: active

       == Contrail config-database ==
       nodemgr: active
       zookeeper: active
       rabbitmq: active
       cassandra: active

       == Contrail webui ==
       web: active
       job: active

       == Contrail analytics-snmp ==
       snmp-collector: active
       nodemgr: active
       topology: active

       == Contrail config ==
       svc-monitor: active
       nodemgr: active
       device-manager: active
       api: active
       schema: active

12. Enter the openstack overcloud update converge command to finalize
    the update.
    
    .. note:: 

       The options used in the openstack overcloud update converge in this
       step will match the options used with the openstack overcloud update
       prepare command entered in step 7.

    ::

       openstack overcloud update converge 
       --templates tripleo-heat-templates/
       --roles-file tripleo-heat-templates/roles_data_contrail_aio.yaml -e
       environment-rhel-registration.yaml -e
       tripleo-heat-templates/extraconfig/pre_deploy/rhel-registration/rhel-registrationresource-registry.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-services.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-net-single.yaml -e
       tripleo-heat-templates/environments/contrail/contrail-plugins.yaml -e
       misc_opts.yaml -e
       contrail-parameters.yaml -e
       docker_registry.yaml

    Monitor screen messages indicating ``SUCCESS`` to confirm that the
    updates made in this step are successful.

 
