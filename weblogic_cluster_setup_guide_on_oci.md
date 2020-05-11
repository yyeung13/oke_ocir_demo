# WebLogic Cluster Setup Guide on OCI

This guide leverages on OCI features to setup a on-prem WebLogic environment on OCI (Oracle Cloud Infrastructure). This is typically applicable if you are using Oracle cloud for development, functional testing or performance testing of on-prem WebLogic applications.

## Overview

In the following steps, you will be setting up a weblogic cluster that spans across two OCI Compute. The steps you will be performing includes:

- Provision first VM (weblogic1) on OCI using standard images  
- Install WebLogic Binary on the first VM  
- Configure a new clustered WebLogic Domain with 2 managed servers  
- Configure node manager for the first VM  
- Clone the boot volume of the first VM to be the boot volume of the second VM  
- Provision second VM (weblogic2) using the boot volume cloned  
- Prepare the second VM to join WebLogic Cluster  
- Bring up and test WebLogic Cluster across two VMs  


## Step 1: Provision first VM weblogic1 on OCI using standard images

First login to your Oracle cloud tenancy from http://cloud.oracle.com with your tenancy ID, username and password.  

Click on the Menu on the top left hand corner and select 'Compute' -> 'Instances'. (Note that it's a good practice to create separate compartment and VCN for multitenancy purpose, this part is not included in the guide here)  

![cluster1](images/weblogic_install/cluster1.jpg]  



## Step 2: Install WebLogic Binary on the first VM

Follow installation guide [here](weblogic_standalone_setup_on_oci.md)

## Step 3: Configure Cluster WebLogic Domain
## Step 4: Configure Node Manager for First VM
## Step 5: Clone Boot Volume of First VM into Second VM
## Step 6: Provision Second VM weblogic2
## Step 7: Prepare Second VM to join WebLogic Cluster
## Step 8: Bring Up and Test WebLogic Cluster
