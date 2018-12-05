.. include:: ../GLOBAL.rst

.. _ansible:

Ansible Integration
===================

Definition and Scope
--------------------
|SCOPE_SCALR| |SCOPE_ACC| |SCOPE_ROLE| |SCOPE_F_ROLE|

Scalr provides built-in integration with Ansible Tower allowing you to add servers built by Scalr to Ansible inventories for configuration management.

.. |at_doc| raw:: html

   <a href="https://docs.ansible.com/ansible/2.5/reference_appendices/tower.html" target="_blank">Ansible Tower Documentation</a>

Find out more about Ansible Tower here: |at_doc| |NEWWIN|

Adding an Ansible Tower Server
------------------------------

Ansible Tower servers can be added at the |Account| or |Scalr| scope. To add an Ansible server click on the Scalr icon on the top left |MENU_ACC|, go down to Ansible Tower and then click on Servers. In the Servers page, click on New Server and you will be prompted for the following:

.. image:: /ansible/images/new_at_servers.png
   :scale: 40%

Adding an Ansible Tower Group
------------------------------

Ansible Tower Groups can also be added at the |Account| or |Scalr| scope. To add an Ansible server click on the Scalr icon on the top left |MENU_ACC|, go down to Ansible Tower and then click on Groups. In the Groups page, click on New Group and you will be prompted for the following:

.. image:: /ansible/images/new_at_group.png
   :scale: 40%

The fields above with the {x} support :ref:`gvi`, by putting a Global Variable in the field you can pull in that value rather than specifying it in the text box.


.. |at_group| raw:: html

   <a href="https://docs.ansible.com/ansible-tower/latest/html/userguide/inventories.html" target="_blank">Click here</a>

|at_group| |NEWWIN| to find out more about Ansible Groups.

Adding an Ansible Tower Bootstrap Configuration
------------------------------------------------

An Ansible Tower Bootstrap Configuration is a template that will tell servers being provisioned by Scalr what Ansible Tower server to connect to and how to join an Organization, Inventory, and Group. All of this information will be pulled into Scalr from the Ansible Tower Server once you select the AT Server field below:

.. image:: /ansible/images/new_at_bootstrap.png
   :scale: 50%

Machine credentials can either be prepopulated in the Ansible Tower Server or generated when selecting the Link Machine Credentials option:

.. image:: /ansible/images/new_at_creds.png
   :scale: 40%

Attaching Bootstrap Configurations to a Server
----------------------------------------------
|SCOPE_ROLE| |SCOPE_F_ROLE|

Bootstrap Configurations can be added in two places:

* The Role scope which would force the configuration on all servers built using that Role.
* The Farm Role scope which would force the configuration on all servers built using that Farm Role.

Role Scope
^^^^^^^^^^^

.. include:: /images_roles/creating_roles.rst
   :start-after: ansible_roles_start
   :end-before: ansible_roles_end

Farm Role Scope
^^^^^^^^^^^^^^^^

The Bootstrap config can also be added directly into a Farm Role. To do this, go into the Farm Role and click on Bootstrap with AT:

.. image:: /ansible/images/at_bootstrap_farmrole.png
   :scale: 70%

At a minimum, this will ensure the server is added to the correct Organization, Inventory and Group. If you want to run a Job Template during the provisioning of the server, click on Orchestration within the Farm Role, New Rule, select the Event, and then click on AT Job:

.. image:: /ansible/images/at_job_farmrole.png
   :scale: 70%

.. warning :: Please note! You must click "Prompt on Launch" on all fields in the Ansible Tower Template for Scalr to import the Job Template.
