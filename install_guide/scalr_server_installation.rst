.. _install:

Scalr Server Install
====================

Prerequisites
^^^^^^^^^^^^^^
* Scalr download token
* Scalr license file

For DEB-based Linux operating systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get the scalr-server package:

.. code-block:: shell

   curl -s https://<token>:@packagecloud.io/install/repositories/scalr/scalr-server-ee/script.deb.sh | sudo bash

Install the package:

.. code-block:: shell

   apt-get install scalr-server

Run the following on only one of the servers when prompted:

.. code-block:: shell

   scalr-server-wizard

The step above did two things, created the /etc/scalr-server directory and the scalr-server-secrets.json file.

Please create the /etc/scalr-server directory on the remaining servers and copy the scalr-server-secrets.json file as well as the license.json file to ALL servers if you haven't done so already.

.. code-block:: shell

   mkdir /etc/scalr-server
   ##Paste the license.json file in the following location on each server:##
   vi /etc/scalr-server/license.json

.. code-block:: shell

   ##Paste the scalr-server-secrets.json in the following location on each server:##
   vi /etc/scalr-server/scalr-server-secrets.json

Create the scalr-server.rb and scalr-server-local.rb configuration file on each server:

.. note::

   Click `here <https://github.com/scalr-tutorials/scalr-server-configuration/tree/master/6-server-ha/>`_ for examples of the scalr-server.rb and scalr-server-local.rb.
   In the scalr-server.rb, replace lines 10-16 with the IP or hostname of your actual servers.
   When placing the scalr-server-local.rb files on their respective servers, make sure they are renamed to scalr-server-local.rb, not the name you see in the Github link. When placing the scalr-server-local.rb on the MySQL Master, be sure to update the following line with the actual slave IP:
   mysql[:repl_allow_connections_from] = 'SLAVE-IP'

.. code-block:: shell

   vi /etc/scalr-server/scalr-server.rb
   vi /etc/scalr-server/scalr-server-local.rb


At this point there should be four files in the /etc/scalr-server directory on each server. The license.json, scalr-server.rb and scalr-server-secrets.json files must be exactly the same on all of the servers, the scalr-server-local.rb file is the only file that is different from server to server:

.. code-block:: shell

   user@scalr:/etc/scalr-server# ls
   license.json  scalr-server-local.rb  scalr-server.rb  scalr-server-secrets.json

.. warning:: The scalr-server.rb, scalr-server-secrets.json and license.json MUST be identical on all servers before running reconfigure

Reconfigure Scalr on the DB node:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure


Reconfigure Scalr on the worker server next:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure


Reconfigure Scalr on the remaining nodes:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure

You can now log into Scalr by putting the hostname or IP address that is listed as your endpoint in the scalr-server.rb into a browser. To log in the first time, please find the admin password in the scalr-server-secrets.json file.


For RPM-based Linux operating systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get the scalr-server package:

.. code-block:: shell

   curl -s https://<token>:@packagecloud.io/install/repositories/scalr/scalr-server-ee/script.rpm.sh | sudo bash

Install the package:

.. code-block:: shell

   yum install scalr-server

Run the following on only one of the servers when prompted:

.. code-block:: shell

   scalr-server-wizard

The step above did two things, created the /etc/scalr-server directory and the scalr-server-secrets.json file.

Please create the /etc/scalr-server directory on the remaining servers and copy the scalr-server-secrets.json file as well as the license.json file to ALL servers if you haven't done so already.

.. code-block:: shell

   mkdir /etc/scalr-server
   ##Paste the license.json file in the following location on each server:##
   vi /etc/scalr-server/license.json

.. code-block:: shell

   ##Paste the scalr-server-secrets.json in the following location on each server:##
   vi /etc/scalr-server/scalr-server-secrets.json

Create the scalr-server.rb and scalr-server-local.rb configuration file on each server:

.. note::

   Click `here <https://github.com/scalr-tutorials/scalr-server-configuration/tree/master/6-server-ha/>`_ for examples of the scalr-server.rb and scalr-server-local.rb.
   In the scalr-server.rb, replace lines 10-16 with the IP or hostname of your actual servers.
   When placing the scalr-server-local.rb files on their respective servers, make sure they are renamed to scalr-server-local.rb, not the name you see in the Github link. When placing the scalr-server-local.rb on the MySQL Master, be sure to update the following line with the actual slave IP:
   mysql[:repl_allow_connections_from] = 'SLAVE-IP'

.. code-block:: shell

   vi /etc/scalr-server/scalr-server.rb
   vi /etc/scalr-server/scalr-server-local.rb


At this point there should be four files in the /etc/scalr-server directory on each server. The license.json, scalr-server.rb and scalr-server-secrets.json files must be exactly the same on all of the servers, the scalr-server-local.rb file is the only file that is different from server to server:

.. code-block:: shell

   user@scalr:/etc/scalr-server# ls
   license.json  scalr-server-local.rb  scalr-server.rb  scalr-server-secrets.json


Reconfigure Scalr on the DB node:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure


Reconfigure Scalr on the worker server next:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure


Reconfigure Scalr on the remaining nodes:

.. code-block:: shell

   /opt/scalr-server/bin/scalr-server-ctl reconfigure

You can now log into Scalr by putting the hostname or IP address that is listed as your endpoint in the scalr-server.rb into a browser. To log in the first time, please find the admin password in the /etc/scalr-server/scalr-server-secrets.json file.

.. code-block:: shell

     "app": {
       "admin_password": "password123",

The default username is admin.
