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

- I would suggest you generate your own download script from edelivery. If you use mine, you will be downloading Oracle Database, Oracle Database Client as well as Oracle Grid Infrastructure within one single download

- If you are using the WGET option above, put the download script under /u01/install and run it with 'download_db.sh'
- Make sure the script is executable, and from /u01/install, run ./download.db.sh

![screenshot2](images/db_install/db2.jpg)  

Note that you need to enter your Oracle download account email address, wait for a few seconds and (no prompt) enter your password  

Should you want to monitor the progress of the download, open another SSH shell and go to /u01/install, and look at the files inside  

![screenshot3](images/db_install/db3.jpg)  

You can tail the log file with 'tail -f <log_file_name>' to see the progress  

![screenshot4](images/db_install/db4.jpg)  

When the download complete, review the files under /u01/install  

![screenshot5](images/db_install/db5.jpg)  

## Step 3: Enable Remote Display for OCI Compute

In order to run the installer, you need to export DISPLAY from OCI to your local laptop first. Follow the guide [here](enable_display_in_oci.md)  


## Step 4: Install Database

- Create a new folder /u01/oracle:  

sudo mkdir /u01/oracle  
sudo chown opc:opc /u01/oracle  
cd /u01/oracle  

- Extract Installer here:  

unzip /u01/install/V982063-01.zip

- Run installer:  

./runInstaller  

![screenshot6](images/db_install/db6.jpg)  

Ignore the error message and wait for DB Installation GUI to show up on your laptop  

- Click 'Next' to install DB software and create a single instance database

![wizard1](images/db_install/wizard1.jpg)  

- Select 'Server Class' since you are running on OCI Compute, and click 'Next'

![wizard2](images/db_install/wizard2.jpg)  

- Select 'Enterprise Edition', and click 'Next'

![wizard3](images/db_install/wizard3.jpg)  

- Create default Base  

sudo mkdir /u01/app  
sudo chown opc:opc /u01/app  

- Accept default Oracle Base and click 'Next'

![wizard4](images/db_install/wizard4.jpg)  

- Accept default Inventory Directory and click 'Next'

![wizard5](images/db_install/wizard5.jpg)  

- Select default and click 'Next'

![wizard6](images/db_install/wizard6.jpg)  

- You can configure database name, SID and PDB name if you want to, the rest of the guide will assume you use default

![wizard7](images/db_install/wizard7.jpg)  

- Accept default configurations options and click 'Next'

![wizard8](images/db_install/wizard8.jpg)  

- Accept default storage on file and click 'Next'

![wizard9](images/db_install/wizard9.jpg)  

- Skip EM Configuration and click 'Next'

![wizard10](images/db_install/wizard10.jpg)  

- Skip Recovery option and click 'Next'

![wizard11](images/db_install/wizard11.jpg)  

- Specify admin passwords and click 'Next'. DO NOT FORGET YOUR PASSWORD  

![wizard12](images/db_install/wizard12.jpg)  

- Accept default operating system groups and click 'Next'

![wizard13](images/db_install/wizard13.jpg)  

- Do not use auto run configuration scripts and click 'Next'

![wizard14](images/db_install/wizard14.jpg)  

- On the screen of Prerequisite Checks, click on 'Fix & Check Again'

![wizard15](images/db_install/wizard15.jpg)  

- A script is generated to fix it, run that script in SSH as follows:

![wizard16](images/db_install/wizard16.jpg)  

Run the script with sudo <full_name_of_the_script>  

![wizard17](images/db_install/wizard17.jpg)  

After running the script, click on 'OK' from the GUI installer.  

Some of the pre-requisites are still missing, install those pre-requisites with the following in SSH:  

sudo yum install oracle-database-server-12cR2-preinstall  

![wizard18](images/db_install/wizard18.jpg)  

Type 'y' to continue the installation.  

Upon completion, go back to installer and click 'Check Again'  

![wizard19](images/db_install/wizard19.jpg)  

Now only issue left is swap size.







- 

Thank you, should you encounter any problems, please feel free to drop me a note at y.yeung@oracle.com.
