Profiling
=========

Installation Guides have content that depends upon the operating
systems.

Use separate files to specify content that is operating-system specific. Each
file should have the same name but with an additional suffix. The following
suffixes should be used:

* ``ubuntu`` for Ubuntu
* ``debian`` for Debian
* ``rdo`` for Red Hat Enterprise Linux and CentOS
* ``obs`` for openSUSE and SUSE Linux Enterprise

.. note::

  Previously, ``.. only`` directive was used to generate conditional output.
  This required multiple builds and has since been phased out.


|

This documentation, is a derivative of `RST conventions <https://docs.openstack.org/doc-contrib-guide/rst-conv.html>`_ by OpenStack, used under CC BY. 