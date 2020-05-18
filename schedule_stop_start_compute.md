# How to Schedule Stop/Start for OCI Compute

Sometimes we want to only use OCI Compute for certain hours of the day and would like to stop the compute outside working hour to optimize cloud investments. This guide explains how you can schedule stop/start of OCI compute. Should you want to perform adhoc stop/start of OCI compute, the easiest way is to use Oracle Cloud Console GUI. This guide explains how to schedule the same task via crontab.

## Overview

Stop/Start OCI Compute can be done via either Oracle Cloud Console GUI or OCI CLI (Command Line Interface). While it's the easiest to perform these task via Cloud Console GUI, scheduling require CLI instead.  

We can either hose the OCI CLI on a small OCI Compute itself, or runs on a Windows Server locally, or a UNIX Server locally. This guide assumes you are running it on a small OCI compute. If you are running on Windows, simply refer to the part about OCI CLI details in this guide, and register the stop/start script with Windows Task Scheduler. For local UNIX, you can skip the part of provisioning OCI Compute in this guide and follow the rest of the steps.  

## Pre-Requisite

You need to have a Oracle OCI Gen 2 account with sufficient credit to provision for the additional compute of 1 OCPU. We are going to use this compute purely for the purpose of scheduler and thus doesn't require much computing power.

## Detailed Steps

### Step 1: Provision OCI Compute for Scheduler

* Login to Oracle Cloud Console from http://cloud.oracle.com and login with your tenancy ID and login credentials
* Click on the burger icon on the top left hand corner and select Compute -> Instances

![scheduler1.jpg](images/oci/scheduler1.jpg)

* Click on 'Create Instance'

![scheduler2.jpg](images/oci/scheduler2.jpg)

* Name the instance as 'scheduler' (You can use other names too) and accept the default image

![scheduler3.jpg](images/oci/scheduler3.jpg)

* Accept the default shape as VM.Standard 2.1 as this only incur one CPU

![scheduler4.jpg](images/oci/scheduler4.jpg)

* Make sure the compute is in your preferred compartment with the appropriate VCN and subnet. We are using a public subnet here so that later we can directly SSH to the instance. If you would like to keep in on a private subnet, you will need to setup Bastion host as a gateway to access the scheduler later. For simplicity here, we use public subnet and create a public IP for this compute

![scheduler5.jpg](images/oci/scheduler5.jpg)

* Ensure you have selected 'Assign Public IP Address'

![scheduler6.jpg](images/oci/scheduler6.jpg)

* Provide a public key. If you need help with key pair generation, refer to https://www.oracle.com/webfolder/technetwork/tutorials/obe/cloud/javaservice/JCS/JCS_SSH/create_sshkey.html. If you are only testing/evaluating and do not want to create your own key pair, this guide also includes a demo key pair [here](keys/readme.MD)

![scheduler7.jpg](images/oci/scheduler7.jpg)

* 






