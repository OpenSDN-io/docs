OpenSDN Operator with Openshift 4.x
===================================

`Code repository <https://github.com/opensdn-io/tf-openshift>`__

:Date: 2021-02-16


Idea and Why Openshift is different from Standard Kubernetes Cluster
--------------------------------------------------------------------

While many developers see practically no difference deploying day-to-day applications on Kubernetes and Openshift,
there are many significant differences between these two platforms.
Openshift deploys every compnent of the cluster as custom Openshift application (etcd server, API server, controller etc.).
Because these components are separately developed for Openshift purposes, its' architecture differes in details from standard Kubernetes clusters.
From Openshift version 4.x all these components are based on operators.
Openshift brings also a lot of features during installation where some of them requires CNI plugin installed from the begining of a cluster.
While OpenSDN may act as CNI plugin for various orchestration platforms, it is challenging to integrate OpenSDN with Openshift platform where CNI plugin is such a vital elements of a whole platform.

Other distinctive problem of Openshift platform is usage of CoreOS nodes.
CoreOS is an operating system designed for orchestrated infrastructures which run containerized workloads.
CoreOS is a read-only filesystem which may be configured only on the boot process and afterward does not allow to change system settings in a runtime.
Ignition configs is a custom way to provide confgiuration for the boot process like what directories, files, systemd services etc. should be created.

Another problem with such a complex platform as Openshift is integration between custom resources.
While platform works only with Openshift components then integration is a natural process developed by the Openshift developers.
But in case when OpenshiftSDN CNI plugin is subsituted with OpenSDN it is challenging to integrate it with other components of the platform - a bit like switching one block in a whole Jenga tower.
For example Openshift will not create network-dependent resources as long as custom resource Network will not have updated status that networking provided by CNI plugin is up and running.

Because of problems listed above in addition to `OpenSDN Operator <https://github.com/opensdn-io/tf-operator>`__ deployment tool it is necessary to provide confgiuration files and manifests specific for Openshift dpeloyment.
`OpenSDN Openshift <https://github.com/opensdn-io/tf-openshift>`__ repository contains all additional files that are required to sucessfully deploy Openshift platform with OpenSDN as CNI plugin.

Find out more on challenges with developing operators for Openshift 4.x `here <https://codilime.com/deploying-a-kubernetes-operator-in-openshift-4-x-platform/>`__ .

Prerequisities
--------------

Deployment depends strongly on Openshift installation which is described in this `documentation <https://docs.openshift.com/container-platform/4.5/installing/installing_aws/installing-aws-customizations.html>`__

Prerequisities that have to be fulfilled in order to dpeloy OpenSDN with operator on Openshift:

* openshift-install binary (>=4.4.8) (`download <https://cloud.redhat.com/openshift/install>`__)
* Openshift pull secrets (`download <https://cloud.redhat.com/openshift/install/pull-secret>`__)
* Any SSH key generated on local machine to provide during installation
* (:Optional: for AWS dpeloyment) Configured AWS account with proper permissions and resolvable base domain configured in Route53 (`documentation <https://docs.openshift.com/container-platform/4.5/installing/installing_aws/installing-aws-account.html#installing-aws-account>`__)
* (:Optional:) `oc` command line tool downloaded (`download <https://cloud.redhat.com/openshift/install>`__)

Deployment
----------

Prepare Install Config
~~~~~~~~~~~~~~~~~~~~~~

Create install config with :command:`./openshift-install create install-config --dir <name of desired directory>`

In created YAML file under specified directory setup all settings of cluster
under *networking* section change *networkType* field to *TungstenFabric* (instead of *OpenshiftSDN*)

.. note::

    Master nodes need larger instances.
    For example, If you run cluster on AWS, use e.g. *m5.2xlarge*.

.. note::
    Refer to this as an example cluster configuration:

    .. code-block:: yaml

        apiVersion: v1
        baseDomain: developer.jnpr.com
        compute:
          - hyperthreading: Enabled
        name: worker
        platform: {}
        replicas: 3
        controlPlane:
        hyperthreading: Enabled
        name: master
        platform:
            aws:
              type: m5.2xlarge
        replicas: 3
        metadata:
        creationTimestamp: null
        name: developer
        networking:
        clusterNetwork:
          - cidr: 10.128.0.0/14
            hostPrefix: 23
        machineCIDR: 10.0.0.0/16
        networkType: TungstenFabric
        serviceNetwork:
          - 172.30.0.0/16
        platform:
          aws:
            region: eu-west-2
        publish: External
        pullSecret: <FILLED AUTOMATICALLY WHEN CREATING INSTALL-CONFIG WITH OPENSHIFT-INSTALL BINARY>
        shKey: <FILLED AUTOMATICALLY WHEN CREATING INSTALL-CONFIG WITH OPENSHIFT-INSTALL BINARY>


Create Openshift manifests
~~~~~~~~~~~~~~~~~~~~~~~~~~

Create Openshift manifests with :command:`./openshift-install create manifests --dir <name of desired directory>`.

In install directories will be created two significant directories:

    * *manifests/* directory stores all YAML manifests that will be aplied on cluster installation
    * *openshift/* directory stores all ignition configs for CoreOS boot process

Install OpenSDN Manifests and Configs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use script from OpenSDN Openshift to automatically apply all manifests and configs into install directory.
`OpenSDN manifests and configs install script <https://github.com/opensdn-io/tf-openshift/blob/master/scripts/apply_install_manifests.sh>`__

Modify Manifests if Neccessary
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you use pod/service network CIDRs other then the default values open the  **<install directory>/manifests/cluster-network-02-config.yml** in text editor and update CIDR values.

Install Openshift
~~~~~~~~~~~~~~~~~

Run this command to start Openshift install: :command:`./openshift-install create cluster --dir <name of openshift install directory>`.
Now Openshift cluster should start installation.
First will be provisoned bootstrap node which will configure master nodes.
After Openshift cluster will be up and running on master nodes, bootstrap node will be shut down and worker nodes will start joining Openshift cluster.

.. warning::

    Follow next steps while Openshift cluster is installing as without it cluster installation will noit succed.

Open Security Groups
~~~~~~~~~~~~~~~~~~~~

Depending on deployment environment ensure that ports necessary for OpenSDN proper work are open.
Especially for cloud environments open additional ports in security groups or firewall rules depending on cloud provider.

To open ports automatically `this <https://github.com/Juniper/contrail-operator/tree/master/deploy/openshift/tools/contrail-sc-open>`__ simple Go CLI tool may be used.

Patch the externalTrafficPolicy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Verify that the **router-default** service has been created, by running :command:`kubectl -n openshift-ingress describe service router-default`

If it is not present yet, wait until it is created. Then patch the externalTrafficPolicy by running this command :command:`kubectl -n openshift-ingress patch service router-default --patch '{"spec": {"externalTrafficPolicy": "Cluster"}}'`

Access Cluster
~~~~~~~~~~~~~~

In order to access export **KUBECONFIG** environment variable.
**KUBECONFIG** file may be found under **<Openshift install directory>/auth/kubeconfig**
E.x.

.. code::

    export KUBECONFIG=<Openshift install directory>/auth/kubeconfig

Afterwards cluster may be accessed with `kubectl` command line tool.

It's also possible to access cluster with dedicated Openshift command line tool: `oc`.
However, `oc` requires to login before.
After successful deployment **openshift-install** binary prints out username (**kubeadmin**) and password to cluster.
Password may be also found also under **<Openshift install directory>/auth/** directory.

Login into `oc` may be performed with this command :command:`oc login -u kubeadmin -p <cluster password>`

Last method to access Openshift cluster is web console.
URL to web console will be displayed by **openshift-install** binary at the end of deployment.
Login into console with the same credentials as for `oc`.

Post-install notes
~~~~~~~~~~~~~~~~~~

OpenSDN Operator creates Persistent Volumes that are used by some of the deployed pods.
After deletion of OpenSDN resources (e.g. after deleting the Manager Custom Resource), those Persistent Volumes will not be deleted.
Administrator has to delete them manually and make sure that directories created by these volumes on cluster nodes are in the expected state.
Example Persistent Volumes deletion command :command:`kubectl delete pv $(kubectl get pv -o=jsonpath='{.items[?(@.spec.storageClassName=="local-storage")].metadata.name}')`
