## Adding Shared Block Storage to Multiple Computes in OCI

This guide explains how to add shared block storage with READ/WRITE access to multiple computes in OCI. If you are looking for adding a dedicated block storage to OCI instead, please refer to [here](add_block_storage_to_oci.md).  

The steps below are based on the blog https://blogs.oracle.com/cloud-infrastructure/using-the-multi-attach-block-volume-feature-to-create-a-shared-file-system-on-oracle-cloud-infrastructure with necessary customization to make sure it's easier to follow and matching OCI Gen 2 environments so that folks that is new to shared file system details can quickly set it up on OCI Gen 2.  

The target architecture we are going to setup as follows from the blog above:  

![architecture](images/block_storage/screen_shot_2018_03_16_at_5_18_30_pm.jfif)

### Pre Requisites

Before using this guide, you need to provide at least two of the nodes above. The guide will assume you have two nodes called 'node1' and 'node2', and you have identified the private IP of both nodes from /etc/hosts.  At the same time, the guide assumes you have sufficient security access (IAM Policy) to manage block storage in your Oracle Cloud Account. For more details on IAM Policy required, please refer to the official document https://docs.cloud.oracle.com/en-us/iaas/Content/Block/Tasks/attachingvolumetomultipleinstances.htm#configcluster and get your administrator to grant the necessary rights if you don't already have it.

### Detailed Steps

#### Step 1: Create Block Volume To Be Shared
#### Step 2: Attached Block Volume to Compute & Run iSCSI Commands
#### Step 3: Setup OCFS2

OCFS2 (https://en.wikipedia.org/wiki/OCFS2) is a shared disk file system to coordinate access to shared disk so as to prevent corruption. We must set it up before using the shared block volume or risk file corruptions.

#### Step 4: Mount Block Volume To Local File System

