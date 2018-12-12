.. include:: ../GLOBAL.rst

Support Cloud Platforms
-----------------------

Scalr can be configured to work with the following cloud platforms and regions.

.. |AWS| image:: images/aws.png
   :scale: 70%

.. |GCP| image:: images/gcp.png
   :scale: 70%

.. |AZURE| image:: images/azure.png
   :scale: 70%

.. |RACKSPACE| image:: images/rackspace.png
   :scale: 70%

.. |IDC| image:: images/idc.png
   :scale: 70%

.. |CLOUDSTACK| image:: images/cloudstack.png
   :scale: 70%

.. |OPENSTACK| image:: images/openstack.png
   :scale: 70%

.. |VMWARE| image:: images/vmware.png
   :scale: 70%

.. |SOFTLAYER| image:: images/softlayer.png
   :scale: 70%

.. csv-table::
   :widths: 20,10,40,10
   :header-rows: 1

   Cloud Provider,Type,Support,Availability
   |AWS|,Public,"All regions, incl. China and GovCloud",GA
   |GCP|,Public,All regions,GA
   |AZURE|,Public,"All regions, Azure Gov, Germany Cloud, Azure Stack",GA
   |RACKSPACE|,Public,All regions,GA
   |IDC|,Public,All regions,GA
   |CLOUDSTACK|,Private,"2.x, 3.x, 4.x",GA
   |OPENSTACK|,Private,"Mitaka, Newton, Ocata, Pike, Queens",GA
   |VMWARE|,Private,"Native VMWare vSphere (5.5, 6.0, 6.5, and 6.7), and VMWare Integrated OpenStack",GA
   |SOFTLAYER|,Private,,GA


.. _supported-os-start:

Supported Server/Instance Operating Systems
-------------------------------------------

Scalarizr is supported on the following operating systems and versions.

========================================    ================================
Operating System                            Supported Versions
========================================    ================================
Ubuntu                                      14.04 - 18.04 LTS
Debian                                      6.x - 7.x
RHEL / CentOS                               5.x - 7.x
Windows Server                              2008 R2, 2012(all), and 2016
========================================    ================================

.. supported-os-end

.. _net-req-start:

Scalarizr Network Requirements
------------------------------

Scalarizr needs to be able to communicate bi-directionally with the Scalr server. This table shows the ports that need to be open to enable this and also shows additional ports required to be open when Scalr is deployed on multiple hosts.

=====   ============   =========================================  =================================
Port    Protocol       Direction                                  Usage
=====   ============   =========================================  =================================
80       TCP           Cloud Instance > Scalr Server              Scalarizr Agent
443      TCP           Cloud Instance > Scalr Server              Scalarizr Agent
3006     TCP           Scalr Server Nodes > DB Nodes              Access to DB's
5671     TCP           Cloud Instance > Scalr Server              Scalarizr Agent (rabbitmq)
6275     TCP           Between Scalr Server Nodes (excluding DB)  RabbitMQ
6276     TCP           Between Scalr Server Nodes (excluding DB)  RabbitMQ
6291     TCP           Between Scalr Server Nodes (excluding DB)  InfluxDB
8008     TCP           Scalr Server > Cloud Instance              Scalarizr Agent (update service)
8010     TCP           Scalr Server > Cloud Instance              Scalarizr Agent (API)
8013     TCP           Scalr Server > Cloud Instance              Scalarizr Agent (control)
11211    TCP           Between the servers running Memcache       Access to Memcache
15671    TCP           Between Scalr Server Nodes (excluding DB)  RabbitMQ
=====   ============   =========================================  =================================

.. net-req-end

.. _scalarizr-func-start:

Scalarizr Functionality
-----------------------

The Scalarizr agent is optional, but without the agent the Scalr experience is limited, please see the following comparison chart:

========================================    ============   ==================
Capability                                   Agentless      Agent Installed
========================================    ============   ==================
Launch, Terminate, Suspend, Resume            Yes             Yes
Security Group management                     Yes             Yes
Tagging                                       Yes             Yes
Autoscaling                                   No              Yes
Orchestration (script execution)              No              Yes
Monitoring                                    No              Yes
Storage Volume addition	                      No              Yes
Software Firewall/ iptables management		    No              Yes
========================================    ============   ==================

.. scalarizr-func-end
