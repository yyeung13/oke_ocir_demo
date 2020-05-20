# Oracle Database 19c Installation on OCI Guide (Standalone DB)

## Pre-requisite

This guide assumes you have already provisioned OEL Compute on OCI and have SSH access to the environment.  

If you are attending instructor-led workshop, please ensure you follow the additional pre-requisites here:  

* Provision a windows server with 4 OCPU on your Oracle cloud account  
* Install Chrome and MobaTerm on the windows server  
* Provision a Oracle Enterprise Linux server with 4 OCPU on the same Oracle cloud account within the same VCN (Virtual Cloud Network) as the windows server  
* Follow instruction on step 2 below to pre-download Oracle Database installation files on this compute to save time (Your instructor will advise how to copy the file to the new compute you will be provisioning in the actual hands on session  

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

- Extract Installer here **Make sure you extract the installer to /u01/oracle as this will be the Oracle Home later**:  

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

- While there are different ways to increase swap, we will only explain one that works on OCI here: By creating a new swap file

- Create new swap file in /u01 in SSH:  

sudo mkdir /u01/swap  

- Create a new file into this new directory with 8Gb in size:  

sudo dd if=/dev/zero of=/u01/swap/swapfile1 bs=1M count=8192  

![wizard20](images/db_install/wizard20.jpg)  

- Create a new swap area on the file that has been created

sudo mkswap  /u01/swap/swapfile1  

- Change the permissions on the file  

sudo chmod 600 /u01/swap/swapfile1  

![wizard21](images/db_install/wizard21.jpg)  


- Add the swapfile to fstab by running: sudo vi /etc/fstab  

Add '/u01/swap/swapfile1    swap   swap      defaults       0 0' based on the below:  

![wizard22](images/db_install/wizard22.jpg)  

- Load the new swap space that had been created for the Instance by running: sudo swapon -a  

- To list the swap devices run the below command: sudo swapon -s  

![wizard23](images/db_install/wizard23.jpg)  

- Now go back to DB installer GUI to re-run the pre-requisite check:  

![wizard24](images/db_install/wizard24.jpg)  

Now click 'Install' to start the installation process.  

- Follow the instruction on screen to run the scripts:  

![wizard25](images/db_install/wizard25.jpg)  

![wizard26](images/db_install/wizard26.jpg)  

Go back to Install GUI and click 'OK' to continue.

- Complete the installation by clicking 'Close'  

![wizard27](images/db_install/wizard27.jpg)  

- Post installation environment setup from SSH  

vi ~/.bash_profile  

Add environment info as follows:  

![wizard28](images/db_install/wizard28.jpg)  

- Before you can access the database, you also need to open up firewall rules within OCI compute:  

sudo firewall-cmd --permanent --zone=public --add-port=1521/tcp  
sudo firewall-cmd --reload  

![wizard31](images/db_install/wizard31.jpg)  


In addition, you need to open up the ingress rule on the subnet where the compute is located from OCI console. Details on how to configure ingress rules can be found here (https://docs.cloud.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm)  

Make sure you specify CIDR block 0.0.0.0/0 and destination prot as 1521.  

- To verify the database is up and running, restart SSH to let environment parameters take effect, and run 'sqlplus sys@orcl as sysdba':

Use the SQLs below to verify the database status:  

Check CDB: select instance_name,status from v$instance;  
Check PDBs: SELECT NAME, OPEN_MODE, RESTRICTED, OPEN_TIME FROM V$PDBS;  


![wizard29](images/db_install/wizard29.jpg)  


- Note the database service name in tnsora.ora in $ORACLE_HOME/network/admin

![wizard30](images/db_install/wizard30.jpg)  

- To connect to the database, e.g., using SQLDeveloper, use the following config (replace hostname with public IP of the database, and use PDB name instead of CDB name in service name):

![wizard32](images/db_install/wizard32.jpg)  

Now your database is up and running and you can connect to it!  


Thank you, should you encounter any problems, please feel free to drop me a note at y.yeung@oracle.com.
