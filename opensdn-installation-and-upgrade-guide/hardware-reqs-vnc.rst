.. _ServerReqAndPlatform:

Server Requirements and Supported Platforms
===========================================

:date: 2020-08-17

This topic discusses server requirements in a OpenSDN
cluster.

Each server must have a minimum of:

-  64 GB memory.

-  300 GB hard drive.

-  4 CPU cores.

-  At least one Ethernet port.

A server can either be a physical device or a virtual machine. For
scalability and availability reasons, it is highly recommended to use
physical servers in most use cases whenever possible.

Server role assignments vary by environment. All non-compute roles can
be configured in each controller node if desired in your topology.

All installation images are available in repositories.

The OpenSDN image includes the following software:

-  All dependent software packages needed to support installation and
   operation of OpenStack and OpenSDN.

-  OpenSDN Controller software – all components.

-  OpenStack release currently in use for OpenSDN.

All components required for installing the OpenSDN Controller are
available for each OpenSDN release, for the supported Linux operating
systems and versions, and for the supported versions of OpenStack.

For a list of supported platforms for all OpenSDN releases,
see `OpenSDN Supported Platforms
List <https://www.juniper.net/documentation/en_US/release-independent/contrail/topics/reference/contrail-supported-platforms.pdf>`__  .

Access ``Container Tags`` are located at 
:ref:`Getting Started with OpenSDN Guide <GettingStarted>`


 
