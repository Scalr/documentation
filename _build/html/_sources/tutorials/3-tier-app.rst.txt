.. include:: ../GLOBAL.rst

3 Tier App Tutorial (cloud agnostic, orchestration automation)
==============================================================

Objective
^^^^^^^^^
This tutorial is designed to guide you through the steps required to deploy a cloud agnostic 3-Tier Web Application using Scalr. This web application will leverage Scalr Orchestration to dynamically configure machines with a cloud native approach.  Any cloud may be used for this exercise, but we recommend the use of a public cloud (AWS, Azure, or GCE) and Ubuntu 16.04 instances to complete this setup.

Application Overview
^^^^^^^^^^^^^^^^^^^^
We will be building a 3-Tier application that uses Nginx as a load balancer to distribute traffic to application servers running a Python application. The Python application uses MySQL as the backend.  Our application lets the user enter messages to store in a MySQL database, and serves back a list of the messages that were entered by the user.  The application is highly available and supports multiple application and database nodes.  Application architecture is outlined below:

.. image:: images/Application-Diagram.jpg
   :scale: 60%

Script Creation
^^^^^^^^^^^^^^^
Create the following scripts in Scalr:

 (Script text files are available here :download:`files.zip`)

- Webapp Deploy
- Webapp Config
- Nginx Install
- Nginx Config
- MySQL Install
- MySQL Config
- MySQL Replication

Roles Required:
^^^^^^^^^^^^^^^
We will only need a Base Ubuntu 16.04 Role.  You may optionally also use two additional Roles with Images that already have MySQL and Nginx installed, but this guide will provide for the use of scripts to install the required software at the time machines are provisioned.

Farm Creation:
^^^^^^^^^^^^^^
* From within your desired |ENVIRONMENT|, create a new farm where we will build our 3 Tier App.
* Populate the Name, Timezone, Team, and Project Fields.

.. image:: images/Screenshot-1.png
   :scale: 60%

* Add a Global Variable at Farm Scope with the name MYSQL_MASTER

.. image:: images/Screenshot-2.png
   :scale: 60%

* Add three Base Ubuntu 16.04 Farm Roles to the Farm.  One for each tier of our application.  When Adding each Farm Role set the desired “Alias” to match the application tier: WebApp, MySQL, and Nginx.

.. image:: images/Screenshot-3.png
   :scale: 50%

Farm Role Configuration
^^^^^^^^^^^^^^^^^^^^^^^
* Please leave any settings not mentioned below as default.  The steps below should cover everything needed for this tutorial.

Select and configure the WebApp farm role:
-------------------------------------------
* Select SCALING and change the autoscaling setting to Min Instances = 2, Max Instances = 5

.. image:: images/Screenshot-4.png
   :scale: 50%

* Select Orchestration and add the following Rules:

===============  ================
Deploy Webapp:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> Web Deploy
Note:            | This will deploy the web app on this server when it provisions.
===============  ================

===============  ================
Config Webapp:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> Web Config
Note:            | This will configure the web app on this server when it provisions.
===============  ================

========================  ================
Config Nginx on Host Up:
========================  ================
Trigger Event:            Host Up
Target:                   Selected Farm Roles -> Nginx Ubuntu
Action:                   Scalr Script -> Nginx Config
Note:                     | This will reconfigure our load balancer on the Nginx servers when this server provisions.
========================  ================

===============================  ================
Config Nginx on Host Terminate:
===============================  ================
Trigger Event:                   Before Host Terminate
Target:                          Selected Farm Roles -> Nginx Ubuntu
Action:                          Scalr Script -> Nginx Config
Note:                            | This will reconfigure our load balancer on the Nginx servers when this server terminates.
===============================  ================

.. image:: images/Screenshot-5.jpg
   :scale: 60%

Select and configure the MySQL farm role:
------------------------------------------
* Select Orchestration and add the following Rules:

===============  ================
Install MySQL:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> MySQL Install
Note:            | This will install MySQL on this server when it provisions.
===============  ================

===============  ================
Config MySQL:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> MySQL Config
Note:            | This will configure MySQL on this server when it provisions.
===============  ================

=========================  ================
Config MySQL Replication:
=========================  ================
Trigger Event:             Before Host Up
Target:                    Triggering Server
Action:                    Scalr Script -> MySQL Replication
Note:                      | This will configure MySQL replication on this server when it provisions.
=========================  ================

=========================  ================
Config Webapp on Host Up:
=========================  ================
Trigger Event:             Host Up
Target:                    Selected Farm Roles ->  Webapp
Action:                    Scalr Script -> Web Config
Note:                      | This will reconfigure the app on WebApp servers so they know when this server provisions.
=========================  ================

================================  ================
Config Webapp on Host Terminate:
================================  ================
Trigger Event:                    Before Host Terminate
Target:                           Selected Farm Roles -> Webapp
Action:                           Scalr Script -> Webapp Config
Note:                             | This will configure the app on WebApp servers so they know when this server terminates.
================================  ================

===========================================  ================
Config MySQL Replication on Host Terminate:
===========================================  ================
Trigger Event:                               Before Host Terminate
Target:                                      Selected Farm Roles -> MySQL Ubuntu
Action:                                      Scalr Script -> MySQL Replication
Note:                                        | This will reconfigure MySQL replication on other MySQL servers when this server terminates.
===========================================  ================

.. image:: images/Screenshot-6.jpg
   :scale: 60%

Select and configure the Nginx farm role:
------------------------------------------
* Select Orchestration and add the following Rules:

===============  ================
Install Nginx:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> Nginx Install
Note:            | This will install Nginx on this server when it provisions.
===============  ================

===============  ================
Config Nginx:
===============  ================
Trigger Event:   Before Host Up
Target:          Triggering Server
Action:          Scalr Script -> Nginx Config
Note:            | This will configure Nginx on this server when it provisions.
===============  ================

.. image:: images/Screenshot-7.png
   :scale: 60%

* Select the SECURITY tab and make sure either your “Default” Security Group has port 80 open, or add/create a new security rule that opens port 80.

.. image:: images/Screenshot-8.jpg
   :scale: 70%

* Save Farm
* Launch Farm

Testing the Application
^^^^^^^^^^^^^^^^^^^^^^^
* Once your servers have all reached a running state, browse to the IP of the Nginx server to access our application.

.. image:: images/Screenshot-9.jpg
   :scale: 90%

The application should look like this:

.. image:: images/Application-Screenshot.jpg
  :scale: 80%

* Test that the application works by writing some values.
* Test increasing the number of Webapp and MySQL servers by increasing the AUTOSCALING Min Instances for each role. Then test scaling back down.

Parameterize the deployment
Parameterized deployments are useful for use cases such as deploying a specific build of an application, or deploying runtime configuration.  We will parameterize our Webapp role with a Global Variable that will allow us to set different release version of our application to be installed.

* Edit your Farm and select the Webapp Role.
* Select the GLOBAL VARIABLES tab and add a new Global Variable with the name APP_BRANCH and a value of feature/reset-database
* Save the farm

.. image:: images/Screenshot-10.jpg
   :scale: 90%

* Terminate your Webapp nodes manually so that new instances will be provisioned with our new configuration.  Alternatively, manually execute the Webapp Deploy script on your Webapp servers.
* Test to see if the new application version is deployed.

.. image:: images/Application-Screenshot-2.jpg
  :scale: 90%
