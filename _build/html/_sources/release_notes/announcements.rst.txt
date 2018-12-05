.. include:: ../GLOBAL.rst

.. _scalr_announcements:

Announcements
==============

Ubuntu 18.04 Support
--------------------

Ubuntu 18.0.4 is now officially supported by Scalarizr. The following software automation tools will be supported:

* Chef
* Ansible

The following built-in automation will not be supported:

* Apache
* Tomcat
* Nginx
* HAProxy
* MySQL
* Percona
* MariaDB
* RabbitMQ
* Redis
* Memcached

Users can still install the platforms above using orchestration scripts or our integration with configuration tools, but it will no longer be included in Scalr built-in automation.

Ubuntu 12.04 Support
--------------------

Ubuntu 12.04 will no longer be supported by the latest Scalarizr versions. We have made some improvements to our APT repository so that each Ubuntu/Debian OS version has its own Scalarizr package. Scalarizr 6.5.2 will be the last version to support Ubuntu 12.04 or less.
