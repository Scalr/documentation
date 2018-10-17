.. _admin_scalarizr:

Scalarizr Installation
=========================

.. Term-Scalarizr-About-Start

Scalarizr is Scalr's management agent. It is optionally installed on every Scalr-managed Server, and used for orchestration and monitoring.

.. include:: /reference/index.rst
   :start-after: scalarizr-func-start:
   :end-before: scalarizr-func-end

.. Term-Scalarizr-About-End

.. include:: /reference/index.rst
   :start-after: supported-os-start:
   :end-before: supported-os-end

.. include:: /reference/index.rst
   :start-after: net-req-start:
   :end-before: net-req-end

Installing Scalarizr
---------------------

How to Install Scalarizr on Linux Images
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Open a shell and execute:

.. code-block:: shell

   PLATFORM=ec2 && curl -L https://<YOUR_SCALR_URL>/public/linux/latest/$PLATFORM/install_scalarizr.sh | sudo bash

Set the PLATFORM variable to the cloud platform that you are using.

===================   ===================
Value                  Cloud Platform
===================   ===================
PLATFORM=ec2	         Amazon EC2
PLATFORM=gce	         Google Compute Engine
PLATFORM=azure	       Microsoft Azure
PLATFORM=openstack	   For any OpenStack based platform
PLATFORM=cloudstack	   CloudStack
PLATFORM=rackspace	   Rackspace Legacy Cloud
PLATFORM=vmware	       VMware
===================   ===================

How to Install Scalarizr on Windows Images
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Open the administrator powershell and execute:

.. code-block:: shell

   iex ((New-Object Net.WebClient).DownloadString('https://<YOUR_SCALR_URL>/public/windows/latest/install_scalarizr.ps1'))

.. note::

  Scalarizr should not be set to automatically start on Windows servers. The Scalarizr update client will automatically start Scalarizr once it has completed the update.

.. _scalarizr_repo:

Scalarizr Logs
--------------

.. include:: /scalarizr/index.rst
   :start-after: Term-Scalr-Logs-Start
   :end-before: Term-Scalr-Logs-Stop

.. _custom_repos:

How to Create Your Own Scalarizr Repo
--------------------------------------

Setting up a Scalr integrated custom repository is very easy.  This capability allows for simple management of your custom repo, the available packages, and your custom release branches right from the Scalr server CLI!  Below are a few key points to note about the "repos" service:

* This service acts as a separate component from other services on the Scalr server.  If desired, it can be put on a dedicated server just like other Scalr components.
* The integrated repo service binds to port 6273 by default.  The Scalr proxy component will forward requests to it if the URI starts with /repos/
* The root dir of the repo is /opt/scalr-server/var/lib/repos, but this may be changed in config.

**Enabling integrated repository tool**

In order to configure the repos service on a multi server Scalr deployment, simply pick one of the app/proxy servers to also act as a repo-server.

Add the following to scalr-server-local.rb on ONE app server:

.. code-block:: shell

   repos[:enable] = true

Add the following to scalr-server.rb on ALL servers.  Be sure to change <ip-of-repo-server> to your selected app server IP:

.. code-block:: shell

   proxy[:repos_upstreams] = ['<ip-of-repo-server>:6273']

   repos[:bind_host] = '<ip-of-repo-server>'

   repos[:bind_port] = 6273

Run scalr-server-ctl reconfigure to ensure the changes are applied.

Managing Scalr Integrated Repo Service
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have added the above to your scalr-server.rb and reconfigured, you will be able to access the service via the command line with "scalr-server-manage agent-repo".  If there are issues with your configuration, they will be noted when you run this command.  If config is correct, the following options will be displayed:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo
   Reading Scalr configuration...
   scalr-server-manage agent-repo status                            - Shows available remote and local repos
   scalr-server-manage agent-repo pull %version%                    - Downloads specified remote repo
   scalr-server-manage agent-repo delete %version%                  - Delete specified local version
   scalr-server-manage agent-repo link %local_name% %local_version% - Links specified local repo name to existing local version
   scalr-server-manage agent-repo unlink %local_name%               - Unlinks specified local repo

Check the status of the service with agent-repo status:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo status
   Reading Scalr configuration...
   Fetching data from remote server...
   Remote versions [linked repo]:
   3.2.11
   3.4.4
   3.5.9
   3.6.8
   3.7.9
   3.8.5
   3.9.9
   3.10.1
   3.11.9
   3.12.7
   4.1.9
   4.2.9
   4.3.9
   4.4.1
   4.5.9
   4.6.6
   4.7.9
   4.8.3
   4.9.9
   4.10.2
   4.11.9
   4.12.2
   5.1.9
   5.2.2
   5.3.9
   5.4.0 [stable]
   5.5.5 [latest]

   Local versions [linked repo]:

   No local versions were found. Use 'scalr-server-manage agent-repo pull %version%' to download.

   No local repos were found. Use 'scalr-server-manage agent-repo link %name% %version%' to create.

Download and Set Up a Custom Repo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As the previous command noted, we do not yet have any packages downloaded.  We will do so now.  The package for download may be referenced either by version number or by branch name:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo pull latest
   Reading Scalr configuration...
   Pulling 5.5.5 RPM packages... [ DONE ]
   Pulling 5.5.5 DEB packages... [ DONE ]
   Pulling 5.5.5 WIN packages... [ DONE ]

Now that we have a local package, we need to create a link between this package and the branch name we want to create and offer to our users.  After successful link, we will be provided with the scalr-server.rb configuration required to enable the new repo:

.. code-block:: shell

  root@ip-111-111-111-111:~# scalr-server-manage agent-repo link TestRepo 5.5.5
  Reading Scalr configuration...
  Repo 'TestRepo' is now linked to version 5.5.5

  Example scalr-server.rb config snippet to enable your repo:

  app[:configuration] = {
    "scalr" => {
      "scalarizr_update" => {
        "mode" => "solo",
        "default_repo" => "TestRepo",
        "repos" => {
          "TestRepo" => {
            "deb_repo_url" => "http://111.111.111.111/repos/apt-plain/TestRepo /",
            "rpm_repo_url" => "http://111.111.111.111/repos/rpm/TestRepo/rhel/$releasever/$basearch",
            "suse_repo_url" => "http://111.111.111.111/repos/rpm/TestRepo/suse/$releasever/$basearch",
            "win_repo_url" => "http://111.111.111.111/repos/win/TestRepo"
          }
        }
      }
    }
  }

Be sure to merge the suggested configuration in to your scalr-server.rb and reconfigure.

Running agent-repo status once more will show the status of our newly configured custom repo service:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo status
   Reading Scalr configuration...
   Fetching data from remote server...
   Remote versions [linked repo]:
   3.2.11
   3.4.4
   3.5.9
   3.6.8
   3.7.9
   3.8.5
   3.9.9
   3.10.1
   3.11.9
   3.12.7
   4.1.9
   4.2.9
   4.3.9
   4.4.1
   4.5.9
   4.6.6
   4.7.9
   4.8.3
   4.9.9
   4.10.2
   4.11.9
   4.12.2
   5.1.9
   5.2.2
   5.3.9
   5.4.0 [stable]
   5.5.5 [latest] [beta]

   Local versions [linked repo]:
   5.5.5 [TestRepo]

Removing Custom Repo Branches and Packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If we want to remove one of our custom repos we must first unlink the custom branch from the package:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo unlink TestRepo
   Reading Scalr configuration...
   Repo 'TestRepo' has been removed

We can then delete the repo files which will remove the files from the server:

.. code-block:: shell

   root@ip-111-111-111-111:~# scalr-server-manage agent-repo delete 5.5.5
   Reading Scalr configuration...
   Version 5.5.5 has been deleted
