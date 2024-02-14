Using Netronome SmartNIC vRouter with OpenSDN
=========================================================

:date: 2021-01-19

.. note::

   The Netronome SmartNIC vRouter technology covered in this document is
   available for evaluation purposes only. It is not intended for
   deployment in production networks.

OpenSDN supports Netronome Agilio CX SmartNICs for OpenSDN
deployment with Red Hat OpenStack Platform Director (RHOSPd) 13
environment.

This feature will enable service providers to improve the forwarding
performance which includes packets per second (PPS) of vRouter. This
will optimize server CPU usage and you can deploy more Virtual network
functions (VNFs) per server.

Benefits:

-  Increased PPS capacity of OpenSDN vRouter datapath allowing
   applications to reach their full processing capacity.

-  Reclaimed CPU cores from OpenSDN vRouter off-loading allowing more
   VMs and VNFs to be deployed per server.

The goal of this topic is to provide a procedure for deploying
accelerated vRouter compute nodes.

Before you begin:

-  Equip compute nodes with Netronome Agilio CX SmartNIC.

   For details, refer to `Agilio CX
   SmartNICs <https://www.netronome.com/products/agilio-cx/>`__.

-  Retrieve *Agilio heat-template plugin*.

   Register on Netronome support site at https://help.netronome.com and
   provide Docker Hub credentials.

   Netronome will provide the TripleO templates for SmartNIC vRouter
   deployment on compute nodes. Also, Netronome will authorize Docker
   Hub registry access.

   For details, refer to `Netronome Agilio vRouter 19\ ``xx`` deployment
   guide <https://github.com/netronome-support/Agilio-vRouter-19xx/wiki/Agilio-vRouter-19xx-deployment-guide-%5BRHEL-7.6%5D%5BRHOSP-13%5D>`__.

-  Note the following version tags:

   ``AGILIO_TAG="2.38-rhel-queensFORWARDER_TAG="2.38-rhel-queens``

Procedure:

.. note::

   If you have multiple undercloud nodes deployed, you must perform the
   following procedure on the same node.

1. Configure *Agilio* plugin.

   For details, refer to `Netronome agilio-ovs-openstack-plugin GitHub
   Repository <https://github.com/Netronome/agilio-ovs-openstack-plugin>`__.

   1. Extract the *Agilio* plugin archive and copy the ``agilio-plugin``
      folder into the ``tripleo-heat-templates`` directory.

      ``[stack@queensa ~]$ tar -xzvf rhosp-contrail-agilio-heat-plugin-5-34.tgzagilio-plugin/agilio-plugin/agilio-vrouter.yamlagilio-plugin/agilio_upgrade.shagilio-plugin/deploy_rhosp.shagilio-plugin/nfp_udev.shagilio-plugin/agilio-env.yamlagilio-plugin/versionagilio-plugin/README.md[stack@queensa ~]$ cp -r agilio-plugin/ tripleo-heat-templates/``

   2. Navigate to the ``agilio-plugin`` directory on the undercloud
      node.

      ``[tripleo-heat-templates]$ cd agilio-plugin/``

   3. Modify ``agilio-env.yaml`` file as per your
      environment.
      
      .. note:: 

         Reserve at least 1375*2 MB hugepages for ``virtio-forwarder``.
      
      Sample ``agilio-env.yaml`` file:
      
      ::

         resource_registry:
           OS::TripleO::NodeExtraConfigPost: agilio-vrouter.yaml

         parameter_defaults:
           # Hugepages
           ContrailVrouterHugepages2MB: "8192"
           # IOMMU
           ComputeParameters:
             KernelArgs: "intel_iommu=on iommu=pt isolcpus=1,2 " 

           ComputeCount: 3

           # Aditional config
           ControlPlaneDefaultRoute: 10.0.x.1
           EC2MetadataIp: 10.0.x.1  # Generally the IP of the Undercloud
           DnsServers: ["8.8.8.8","192.168.3.3"]
           NtpServer: ntp.is.co.za
           ContrailRegistryInsecure: true
           DockerInsecureRegistryAddress: 172.x.x.150:6666,10.0.x.1:8787
           ContrailRegistry: 172.x.x.150:6666
           ContrailImageTag: <container_tag>-rhel-queens

         # Fix DB Diskspace too low issue
           ContrailAnalyticsDBMinDiskGB: 40

   4. Add Docker Hub credentials to
      ``tripleo-heat-templates/agilio-plugin/agililo_upgrade.sh`` file
      to retrieve containers from
      ``AGILIO_REPO="docker.io/netronomesystems/"`` repository.

      ``#GENERAL DOCKER CONFIGDOCKER_USR=****** #ENTER_DOCKER_USERNAME_HEREDOCKER_PASS=****** #ENTER_DOCKER_PASSWORD_HERE``

      ``[root@overcloud-novacompute-2 heat-admin]# docker ps -a | grep virtio_for7d5af8a2591d        docker.io/netronomesystems/virtio-forwarder:2.38-rhel-queens           "./entrypoint.sh"        30 seconds ago      Up 15 seconds virtio_forwarder``

      ``[root@overcloud-novacompute-2 heat-admin]# docker ps -a | grep agilioc7c611b5168b        docker.io/netronomesystems/agilio-vrouter:2.38-rhel-queens             "./entrypoint.sh"        46 seconds ago      Up 38 seconds agilio_vrouter``

2. Prepare the OpenSDN cluster for deployment.

   .. note::

      Do not perform steps for :ref:`Setting Up the Overcloud RHOSP 13`.

3. Deploy the cluster by one of the following ways:

   -  Add ``agilio-env.yaml`` to installing overcloud step as mentioned
      in :ref:`Setting Up the Overcloud RHOSP 13` topic.

      ``openstack overcloud deploy --templates ~/tripleo-heat-templates-e ~/overcloud_images.yaml-e ~/tripleo-heat-templates/environments/network-isolation.yaml-e ~/tripleo-heat-templates/environments/contrail/contrail-plugins.yaml-e ~/tripleo-heat-templates/environments/contrail/contrail-services.yaml-e ~/tripleo-heat-templates/environments/contrail/contrail-net.yaml-e ~/tripleo-heat-templates/agilio-plugin/agilio-env.yaml--roles-file ~/tripleo-heat-templates/roles_data_contrail_aio.yaml``

      Or

   -  Run the following command:

      ``deploy_rhosp.sh``

      ``-e ~/tripleo-heat-templates/agilio-plugin/agilio-env.yaml``

On completing above steps successfully, refer to `Netronome
agilio-ovs-openstack-plugin GitHub
Repository <https://github.com/Netronome/agilio-ovs-openstack-plugin>`__
on how to spin up the accelerated VMs.

 
