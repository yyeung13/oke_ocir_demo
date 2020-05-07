# Oracle Database 19c Installation on OCI Guide (Standalone DB)

## Pre-requisite

This guide assumes you have already provisioned OEL Compute on OCI and have SSH access to the environment

## Step 1: Add Addition Block Storage to Compute

The default boot volume for the VM is only 47GB and may not be enough for database. While you can continue to install database on the default boot volume of OCI, it's always better to create additional block storage for Database Installation as the subsequent data file may grow big, whether you are using the database for production or performance testing purpose.  

Follow the guide [here](add_block_storage_to_oci.md) and map the new folder as /u01 following the guide.



## Step 2: Download Oracle Database 19c Binary

- Secure Shell into OCI compute and login as opc, you should not need to enter password since we are using the generated private key
- Create folder under /u01 to upload the installer and grant ownership to user opc  

sudo mkdir /u01/install  
sudo chown opc:opc /u01/install  

![screenshot1](images/db_install/db1.jpg)



Thank you, should you encounter any problems, please feel free to drop me a note at y.yeung@oracle.com.
