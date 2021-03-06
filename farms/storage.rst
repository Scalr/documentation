.. include:: ../GLOBAL.rst

.. _storage:

Configuring Storage
===================

|SCOPE_ENV|

Storage is configured at the Farm Role and includes options for configuring the mandatory root storage volume and optional additional storage. The additional storage can be shared on some clouds and marked as reusable. Reusable storage is automatically preserved by a snapshot and added to new servers when they are launched. In AWS this includes copying snapshots to other Availability Zones when a Farm Role is re-launched in a different AZ.

.. note:: Storage configuration changes do not apply to running Servers. They only take effect when new servers are launched.

Below is an example of a Storage configuration for a root volume. The actual options available are specific to the Cloud allocated to the Farm Role but are typically limited to the volume size and type.

.. image:: images/storage_root.png
           :scale: 50%

For additional storage there are many more options available which are again specific to the Cloud allocated to the Farm Role. This example for AWS shows the full set of options when auto-mount is enabled.

.. image:: images/storage_add.png
           :scale: 50%

The common parameters for additional storage are as follows.

.. csv-table::
   :header-rows: 1
   :widths: 25,100
   :file: csv/storage_add_gen.csv

Volume Mounting
---------------

The way volumes are mounted varies depending on the Operating System.

It is important to be aware that the :ref:`EBSVolumeMounted` can fire when volumes are remounted during a reboot. Thus any Orchestration Rules that are trigged by the :ref:`EBSVolumeMounted` must be coded to be aware of this possibility and not take any inappropriate actions after reboots that could cause data loss.

For ALL Operating Systems additional volumes (not root / C:) are mounted by the scalr agent (scalarizr) on the first boot of a Server and will trigger the :ref:`EBSVolumeMounted`. For subsequent reboots the additional volumes are mounted as follows.

**Linux**

On first boot scalarizr adds the additional volumes to the operating systems fstab file, e.g. /etc/fstab so that the Operating System itself will remount the volumes on subsequent reboots. Thus the :ref:`EBSVolumeMounted` event will NOT fire for additional volumes after a reboot.

However if scalarizr detects that a volume configured in Scalr has not been re-mounted after a reboot, e.g. because the entry was removed from fstab, then it will re-mount the volume and add it back into fstab. In these circumstances the :ref:`EBSVolumeMounted` WILL fire.

**Windows**

Scalarizr performs all required volume mounting on first boot and all subsequent re-boots. Thus the :ref:`EBSVolumeMounted` WILL fire on every re-boot.

.. note:: Scalarizr checks volumes every time it restarts, so if any manual adjustments have been made since the server booted, scalarizr will unmount and remount volumes to ensure the Scalr configuration is re-applied. The :ref:`EBSVolumeMounted` will fire for these adjustments. If you wish to change the configuration of Volumes you must do this through Scalr.

AWS Storage Configuration
-------------------------

Additional AWS EBS Volumes can be configured on a Farm Role to be added to every server. The additional EBS options are as follows.

.. |EBS| raw:: html

   <a href="https://aws.amazon.com/ebs/details/" target="_blank">Amazon EBS Product Details</a>

.. csv-table::
   :header-rows: 1
   :widths: 25,100

   Option,Description
   EBS Type,The full range of EBS types can be selected. See |EBS| |NEWWIN| for more details
   Snapshot,The Snapshot to create the volume from. If blank a new Volume is created
   Enable EBS encryption,Turn on encryption and specify the KMS key

.. image:: images/storage_aws.png
   :scale: 50%

All storage volumes for AWS can be managed in the EBS Volumes page. Go to the main Scalr menu dropdown on the top left |MENU_ENV| and click on AWS, then EC2, and EBS volumes:

.. image:: images/storage_aws2.png
   :scale: 50%

From the screen above you can do the following:

* Create and attach storage
* Change existing volume settings: size, type, tags
* Check Cloud Watch statistics
* Snapshot the selected volume
* Setup automatic snapshots for a volume

.. |aws_storage_link1| raw:: html

   <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html" target="_blank">Detaching Storage to Modify Volumes</a>

.. |aws_storage_link2| raw:: html

    <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/stop-start.html" target="_blank">Starting and Stopping an Instance to Modify an EBS Volume</a>

If you are planning on extending storage on a volume on a running server in AWS, the volume may need to be detached and/or the server should be stopped prior to doing this. Please see the AWS guidance on this:

* |aws_storage_link1| |NEWWIN|
* |aws_storage_link2| |NEWWIN|

To extend storage, go to the AWS Volumes page, find your volume and click on the gear:

.. image:: images/storage_aws3.png
   :scale: 70%

.. note:: For Linux, Scalr supports extending filesystems for Ext4/XFS volumes. For Windows, Scalr supports extending filesystems for suspended servers that are Windows 2012 or higher and NTFS.

EBS Snapshots can also be taken from within the Servers page by clicking on the actions menu and going down to Create Server Snapshot:

.. image:: images/server_list.png
           :scale: 50%

On this page you can create a snapshot/image of the root volume as well as snapshots of all volumes attached to the server:

.. image:: images/create_snapshot.png
           :scale: 65%


Google Cloud Platform Storage Configuration
-------------------------------------------

GCP based Farm Roles can have additional storage added using either "Persistent Disk" or "Local SSD (ephemeral)".

**Persistent Disk** : Options are Standard or SSD disk |BR|
**Local SSD** : Only option is the name.

.. |GCP1| image:: images/storage_gcp_1.png
          :scale: 50%

.. |GCP2| image:: images/storage_gcp_2.png
          :scale: 50%

|GCP1| |GCP2|

All storage volumes for GCP can be managed in the GCP Persistent Disk page. Go to the main Scalr menu dropdown on the top left |MENU_ENV|, click on Google Cloud Platform, then Compute Engine, and Persistent Disks:

.. image:: images/storage_gcp_3.png
   :scale: 50%

From the screen above you can do the following:

* Change the size of the existing disks

Azure Storage Configuration
---------------------------

Azure based Farm Roles can have additional managed data disks added. The only option is the size.

.. image:: images/storage_azure.png
   :scale: 50%

All storage volumes for Azure can be managed in the Azure Managed Disk page. Go to the main Scalr menu dropdown on the top left |MENU_ENV|, click on Azure and Managed Disks:

.. image:: images/storage_azure2.png
   :scale: 50%

From the screen above you can do the following:

* Create and attached a new Managed Disk
* Detach a managed disk from a server
* Update the size and type of existing disks (if the disk is not attached)
* Snapshot of the selected volume

If you are planning on extending storage on a volume on a running server in Azure, the volume may need to be detached and/or the server should be stopped prior to doing this. Please see the Azure guidance on this:

.. |azure_storage_link1| raw:: html

   <a href="https://docs.microsoft.com/en-us/azure/virtual-machines/linux/expand-disks" target="_blank">Deallocate Server to Modify Volumes</a>

* |azure_storage_link1| |NEWWIN|

.. note:: For Linux, Scalr supports extending filesystems for Ext4/XFS volumes. For Windows, Scalr supports extending filesystems for suspended servers that are Windows 2012 or higher and NTFS.

OpenStack Storage Configuration
-------------------------------

OpenStack based Farm Roles can have additional persistent disks added. The only option is the size.

.. image:: images/storage_openstack.png
   :scale: 50%

All storage volumes for Openstack can be managed in the Openstack Volumes page. Go to the main Scalr menu dropdown on the top left |MENU_ENV|, click on Openstack and then Volumes:

.. image:: images/storage_openstack2.png
   :scale: 50%

From the screen above you can do the following:

* Create and attach storage
* Change existing volume settings: size, type, tags
* Snapshot the selected volume
* Setup automatic snapshots for a volume

VMWare Storage Configuration
----------------------------

VMware based Farm Roles can have additional virtual disks added. The Volume Settings available are as follows.

.. csv-table::
   :header-rows: 1
   :widths: 25,100

   Option,Description
   Provisioning,Defines how to initialise the volume using zeroing
   Shared Disk,Allows the disk to be shared across all servers in the Farm Role. Only available for "Eager Zeroed" provisioning
   Datastore,Select the datastore that the extra volume should be provisioned to. If the placement strategy is "auto" then all volumes will be placed on the datastore selected by Scalr.

.. image:: images/storage_vmware.png
   :scale: 85%

All storage volumes for VMware can be managed in the VMware Virtual Disks page. Go to the main Scalr menu dropdown on the top left |MENU_ENV|, click on VMware and then Virtual Disks:

.. image:: images/storage_vmware2.png
   :scale: 60%

From the screen above you can do the following:

* Create and attach storage

If you are planning on extending storage on a volume on a running server in VMware, the server should be stopped prior to doing this. To extend storage, go to the VMware Virtual Disks page, find your volume and click on the gear:

.. image:: images/vmware_extend_storage.png
   :scale: 70%

.. note:: For Linux, Scalr supports extending filesystems for Ext4/XFS volumes. For Windows, Scalr supports extending filesystems for suspended servers that are Windows 2012 or higher and NTFS.
