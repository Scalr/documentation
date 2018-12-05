.. include:: ../GLOBAL.rst

Connecting Scalr to AWS
========================

Scalr needs access to your Cloud Platform(s) in order to provision and manage infrastructure on your behalf. To achieve this there is configuration work to do in both AWS and Scalr as follows.

1. Configure AWS VPC's and Detailed Billing (Optional)
2. Configure Instance Connection Policy
3. Create/Configure a user and access keys in AWS
4. Add AWS credentials to Scalr and link to |ENVIRONMENTS|.

.. |iam_link| raw:: html

   <a href="https://aws.amazon.com/documentation/iam/" target="_blank">AWS Identity and Access Management Documentation</a>

If you need assistance creating an AWS user and the required policy please refer to |iam_link| |NEWWIN|.

.. note::
   | **Your credentials are safe with us**
   | Credentials are stored securely and encrypted. Scalr will not use your Credentials for any purpose you have not agreed to. Our terms of service can be found on the Scalr Website Policies Page.
   | **Why Does Scalr Need Credentials?**
   | Cloud Credentials are needed by Scalr to provision and manage cloud infrastructure on your behalf.

Configure AWS for Scalr
-----------------------

.. |vpc_link| raw:: html

   <a href="https://aws.amazon.com/documentation/vpc/" target="_blank">Amazon Virtual Private Cloud Documentation</a>

Before creating access keys and connecting Scalr to AWS there are some configuration considerations that may need to implemented to ensure Scalr can connect to your instances in AWS.

Configure Virtual Private Cloud (VPC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. |peer_link| raw:: html

   <a href="https://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/peering-configurations.html" target="_blank">VPC Peering Configurations</a>

You will need to create or configure an AWS VPC to work with Scalr. Every AWS user has access to the EC2-Classic network but this network is shared with all other customers in the region and is typically NOT an acceptable network for most use cases. If you need to create a VPC for Scalr and the Scalr managed instances please refer to |vpc_link| |NEWWIN|.

Configure Instance Connection Policy
------------------------------------

You will also need to perform some additional configuration work to ensure that Scalr can connect to your AWS instances. There are three options for this.

#. **Public** - Scalr will ONLY connect to the Public IP of instances. Only use this option if ALL instances managed by Scalr will have a Public IP.
#. **Local** - Scalr will ONLY connect to the local/private IP of instances. This option is only possible if one of the following scenarios applies.

   #. All your managed instances are in the same VPC as Scalr.
   #. You have routing or peering connections to all the other VPC's where Scalr managed instances reside. See |peer_link| |NEWWIN| for more details.
   #. You have VPN connection(s) to all the other VPC's where Scalr managed instances reside.

#. **Auto** - (default) Scalr will connect to the Public IP of an instance if it has one, otherwise it will fall back to the local/private IP.

If you choose to use the "public" or "local" option then you need to login to your Scalr server and update the configuration in ``/etc/scalr-server/scalr-server.rb`` by adding the following entry. (NOTE: this entry must be merged with any exiting entries in the ```app[:configuration] = ``` structure)

.. code-block:: python

   app[:configuration] = {
       "scalr" => {
         "aws" => {
           "instances_connection_policy" => "local" or "public"
          }
       }
   }

After adding this entry you must re-configure scalr by running ``sudo scalr-server-ctl reconfigure``.

Configure Detailed Billing
--------------------------

Scalr supports detailed billing when using AWS. Detailed billing will give you more details than just showing the total price for compute.

To enable detailed billing, you need to activate it in AWS and enter required details into Scalr.

.. warning:: Even if you might have detailed billing enabled in AWS already, you must follow the instructions bellow to make sure the reports from AWS get generated in the correct format that Scalr needs.

.. note::
    * Please note that the billing details will be processed with a 8-12 hours delay.  This isn't a Scalr delay, but rather AWS posts updated billing information every 8 hours. As soon as it is posted, it becomes possible for us to process it and include in our reports.
    * If you enable detailed billing, custom Pricing List entries for AWS will be ignored as AWS will be providing the final cost (usage) that we report against.  To add sub-charges with detailed billing enabled you must use Markups.

.. |BILL_LINK| raw:: html

   <a href="https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-getting-started.html#step-2" target="_blank">AWS Billing and Cost Management - Getting Started - Step 2: Turn on Reports</a>

You will need to create an S3 bucket to store the billing files. Please refer to |BILL_LINK| |NEWWIN| for details on creating the S3 bucket and setting permissions.

1. Login to the AWS Console.
2. Open the profile menu in the top right and click on **My Billing Dashboard**.

.. image:: images/aws_bill_1.png
   :scale: 50%

3. Click on Preferences in the left menu.
4. Make sure Receive Billing Reports is checked.
5. Enter a S3 bucket name to store your reports in.
6. Click on Verify to make sure the bucket exists and has correct permissions.
7. Make sure that Detailed billing report with resources and tags is checked.
8. Click on Save preferences.

.. image:: images/aws_bill_2.png
   :scale: 50%

9. Click on Manage report tags.
10. Make sure that scalr-meta is Active and click Save.

.. image:: images/aws_bill_3.png
   :scale: 50%

.. note:: If scalr-meta is not present, ensure that you have launched at least one Farm Role in order for Scalr to create the correct tags for AWS resources.  There will be an 8-10 hour delay on initial billing details processing.

Configuring your AWS IAM User
-----------------------------

For Scalr to work with AWS we recommend creating/using an IAM User that has at least these minimum permissions set.

.. code-block:: json

 {
     "Version": "2012-10-17",
     "Statement": [
       {
           "Action": "iam:GetUser",
           "Resource": "*",
           "Effect": "Allow"
       },
       {
           "Action": "iam:ListInstanceProfiles",
           "Resource": "*",
           "Effect": "Allow"
       },
       {
           "Action": "iam:ListServerCertificates",
           "Resource": "*",
           "Effect": "Allow"
       },
       {
          "Action": "iam:PassRole",
           "Effect": "Allow",
           "Resource": "*"
       },
       {
           "NotAction": "iam:*",
           "Resource": "*",
           "Effect": "Allow"
       }
     ]
   }

Adding AWS Credentials to Scalr
-------------------------------

First you need to create and obtain the required access keys from AWS IAM.

1. Login to the AWS Management Console.
2. Select the IAM Service.
3. Navigate to Users, select the user to use with Scalr and click on the "Security Credentials" Tab.
4. Click on "Create Access Key" and copy the Access Key ID and Secret Access Key.

.. include:: credentials-generic.rst

After selecting Add Credentials, you will be prompted to configure your Cloud Credentials and properties:

.. image:: images/AWS-creds.png
   :scale: 40%

.. image:: images/AWS-Keys.png
   :scale: 40%

.. image:: images/AWS-Keys-Scalr.png
   :scale: 40%

5. Check "Enable Detailed Billing" if you require Scalr to collect detailed billing information for Cost Manager/Analytics. Enter the region and S3 Bucket name previously configured in AWS.

.. image:: images/aws-detailed-bill.png
   :scale: 40%

6. Click Save and your screen should look similar like this after Scalr has validate the Credentials.

.. image:: images/AWS-Keys-Scalr-Saved.png
   :scale: 40%

Validating the Connection to AWS
--------------------------------

You can now proceed to adding these credentials to your |ENVIRONMENTS|.
