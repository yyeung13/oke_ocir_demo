## Adding Shared Block Storage to Multiple Computes in OCI

This guide explains how to add shared block storage with READ/WRITE access to multiple computes in OCI. If you are looking for adding a dedicated block storage to OCI instead, please refer to [here](add_block_storage_to_oci.md).  

The steps below are based on the blog https://blogs.oracle.com/cloud-infrastructure/using-the-multi-attach-block-volume-feature-to-create-a-shared-file-system-on-oracle-cloud-infrastructure with necessary customization to make sure it's easier to follow and matching OCI Gen 2 environments so that folks that is new to shared file system details can quickly set it up on OCI Gen 2.  

The target architecture we are going to setup as follows from the blog above:  

![architecture](images/block_storage/screen_shot_2018_03_16_at_5_18_30_pm.jfif)

### Pre Requisites

Before using this guide, 
