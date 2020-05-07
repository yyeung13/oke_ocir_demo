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

- Upload database installer to /u01/install folder. If you are using MobaTerm, simply use the upload feature that comes with MobaTerm. Otherwise, you can use another other tool. If you are downloading from edelivery (http://edelivery.oracle.com)), note that there is a wget feature that allows you to generate a script and download DB installer directly to the compute instance, which is a lot faster. Make sure you login with your Oracle account when you run the wget generated download scripts. Sample download script is [here](images/db_install/download_db.sh)


- If you are using the WGET option above, put the download script under /u01/install and run it with 'download_db.sh'
- Make sure the script is executable, and from /u01/install, run ./download.db.sh

![screenshot2](images/db_install/db2.jpg)  

Note that you need to enter your Oracle download account email address, wait for a few seconds and (no prompt) enter your password  

Should you want to monitor the progress of the download, open another SSH shell and go to /u01/install, and look at the files inside  

![screenshot3](images/db_install/db3.jpg)  

You can tail the log file with 'tail -f <log_file_name>' to see the progress  

![screenshot4](images/db_install/db4.jpg)  



Thank you, should you encounter any problems, please feel free to drop me a note at y.yeung@oracle.com.
