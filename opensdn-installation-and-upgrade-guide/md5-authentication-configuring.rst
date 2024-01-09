Configuring MD5 Authentication for BGP Sessions
===============================================

:date: 2019-04-23

OpenSDN supports MD5 authentication for BGP peering based on RFC 2385.

This option allows BGP to protect itself against the introduction of
spoofed TCP segments into the connection stream. Both of the BGP peers
must be configured with the same MD5 key. Once configured, each BGP peer
adds a 16-byte MD5 digest to the TCP header of every segment that it
sends. This digest is produced by applying the MD5 algorithm on various
parts of the TCP segment. Upon receiving a signed segment, the receiver
validates it by calculating its own digest from the same data (using its
own key) and compares the two digests. For valid segments, the
comparison is successful since both sides know the key.

The following are ways to enable BGP MD5 authentication and set the keys
on the OpenSDN node.

1. If the ``md5`` key is not included in the provisioning, and the node
   is already provisioned, you can run the following script with an
   argument for md5:
   
   ::

      contrail-controller/src/config/utils/provision_control.py

      host@<your_node>:/opt/contrail/utils# python provision_control.py --host_name <host_name> --host_ip <host_ip> --router_asn <asn> --api_server_ip <api_ip> --api_server_port <api_port> --oper add --md5 “juniper” --admin_user admin --admin_password <password>  --admin_tenant_name admin

2. You can also use the web user interface to configure MD5.

   1. Connect to the node’s IP address at port 8080 (<node_ip>:8080) and
      select :menuselection:`Configure->Infrastructure->BGP Routers`. As shown in
      Figure 1, a list of BGP peers is displayed.

      |Figure 1: Edit BGP Router Window|

   2. For a BGP peer, click on the gear icon on the right hand side of
      the peer entry. Then click :guilabel:`Edit`. This displays the Edit BGP
      Router dialog box.

   3. Scroll down the window and select :guilabel:`Advanced Options`.

   4. Configure the MD5 authentication by selecting :menuselection:`Authentication
      Mode->MD5` and entering the **Authentication Key** value.
 

.. |Figure 1: Edit BGP Router Window| image:: images/s042480.png
