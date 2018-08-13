.. include:: ../GLOBAL.rst

.. _roles:

Creating Roles
==============
|SCOPE_SCALR| |SCOPE_ACC| |SCOPE_ENV|

Step two of using Scalr to configure and deploy infrastructure is to create Roles. Roles are the building block that define the characteristics of servers that are deployed by Scalr. In their simplest form Roles provide an abstraction layer over the top of the images. A Role is linked to one or more similar Images, located in different clouds and/or locations, thus providing the flexibility for infrastructure designers and end users to chose the cloud and location for the resulting servers at the time they launch an application.

"Similar" images are images that are configured to provide the same behaviour and deliver the same service. Thus all similar images will have

* The same base operating system and Version
* The same patch levels
* The same pre-installed and configured software packages

Roles can also include options to further manage the behaviour and configuration of servers.

* Orchestration rules that define actions (executed through Scripts) that take place at various lifecycle events throughout the life of a server.
* Global Variables that can be used to pass dynamic values to alter the configuration of servers and execution of Orchestration rules.
* Integration with Chef to enable Chef recipes to perform the bootstrap and configuration of servers.

Roles can be created at any scope in Scalr and made available to lower scopes. |ACCOUNT| scope roles can be given permissions to restrict which |ENVIRONMENTS| they are available to. By default they will be available to all |ENVIRONMENTS| in the |ACCOUNT|.

Role functionality is accessed via Roles in the Main menu or Bookmarks bar at any scope. Note that you can jump straight to New Role from the main menu or click on "Roles" and then "New Role" at the top of the list.

.. |ROLE_MENU| image:: images/role_menu.png
              :scale: 50%

.. |ROLE_LIST| image:: images/role_list.png
              :scale: 50%

|ROLE_MENU| |ROLE_LIST|

You will now be presented with the New Role screen where you configure all the Role options

.. image:: images/new_role.png
   :scale: 50%

.. csv-table:: Role Configuration Parameters
   :header-rows: 1
   :widths: 15,10,75

   Parameter,Mandatory?,Details
   Name,Yes,A meaningful name that typically includes the OS and version and main package e.g. "ubuntu-1404-haproxy"
   OS Family and Version,Yes,This must match the OS Family and Version of the Images you wish to link to this role
   Category,Yes,Provides grouping of roles when being selected in the Farm Designer. You can add your own Categories from Roles -> Role Categories menu
   Description,No,Describes the purpose of the Role and any special details that may be relevant
   Tags,No,You can include previously defined :ref:`policy_tags` to associate the Role with governance policies defined in the :ref:`policy_engine`. You can also add your own tags to help with role grouping related Roles and for use in :ref:`cost_management` for cost reporting.
   Quick Start,No,Enable this to include this role in the Quick Start menu of the Farm Designer
   Use Scalr-Agent,No,Enabled by default and required for `Role Orchestration`_ and :ref:`auto_scaling`. If this is disabled the "Configure Built-In Automation wheel" |CONFIGURE| will be disabled but the option to Use Cloud-Init will appear.
   Configure Built-In Automation wheel |CONFIGURE|,No,Click on the wheel and select "Chef" to enable the Chef options for this role. This will make the Chef tab visible on the left side. You can only enable Chef when creating a new Role as it cannot be done after the role has been saved. See Note below.
   Use Cloud-Init,No,This option only appears if "Use Scalr-Agent" is disabled. Enable this option to have Scalr work with Cloud-Init to configure servers. Once enabled the Cocnfigure button |CONFIGURE| will appear to enable you to set the Cloud-Int Config
   Images,Yes,You must link the Role to at least one Image (see below). When you click on ADD IMAGES you will only see images with the same OS Family/Version that have Scalarizer or CloudInit installed.

.. note:: Built in Automations other than Chef will be deprecated in future release of Scalr. They will continue to work for existing roles but should now be replaced by user configured Orchestration Rules.

When adding Images to Roles you need to be aware of the different ways Images are configured in each cloud.

.. csv-table::
   :widths: 15,70
   :header-rows: 1

   Cloud,Image Configuration
   AWS EC2,Images are unique for every AWS location. You must add every similar image in every location you want the Role to be enabled for.
   Azure,Azure Image is common to all regions so only one Image needs be added to the Role
   GCP,GCP Image is common to all regions so only one Image needs be added to the Role
   Openstack,Images are unique for every Openstack. You must add every similar image in every region you want the Role to be enable for.
   VMware,Images are unique for every VMware location. You must add every similar image in every location you want the Role to be enable for.

Editing Roles
-------------

To edit a role navigate to the Roles Library (Main Menu -> Roles), select the Role to be edited and click the Edit |EDIT| button.

.. _role_orchestration:

Role Orchestration
==================

Orchestration rules define actions to be performed at specific events during the lifecycle of servers. Orchestration rules associated with a Role are inherited by :ref:`farm_roles` that are created in the Farm designer or Farms created via :ref:`service_catalog`.

To configure Orchestration Rules click on the Orchestration tab on the left side of the Role details screen.

.. image:: images/role_orch.png
   :scale: 50%

.. include:: /farms/orchestration_include.rst

Global Variables in Roles
=========================

When defining Roles you can configure aspects of the way :ref:`gvs` are used in the Role.


Ansible Tower in Roles
======================

.. Include file from Ansible Tower page

Chef in Roles
=============

.. Include file from Chef page
