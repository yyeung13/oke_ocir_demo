## How to Add Block Storage to OCI

The default VM boot volume in OCI is 46.6GB and may not be sufficient for our actual requirement. This guide explains how to add additional block volume and mount to OCI compute so that you can add additional storage to OCI.  


### Step 1: Create Block Volume

- First, login to OCI console from http://cloud.oracle.com with your tenancy ID and ID/password
- Click on the burger icon on the top left hand corner and select 'Block Storage' -> 'Block Volumes'
- Make sure you select the right compartment fromt he left hand side menu under Compartment drop down, so that the block volume is created under the right compartment
- Click on 'Create Block Volume'
- Provide name, size of the block volume and click on 'Create Block Volume'. For simplicity, leave the other fields as default.
- Within 10-20 seconds, your block volume should be ready.


### Step 2: Attach Block Volume to Instance

- From the left hand side menu after block volume creation, click on 'Attached Instances'
- Click on the button 'Attach Instance' on the right hand screen
- While leaving the other fields default for simplicity, choose the instance to attach to in Instance drop down, and choose a device name in drop down. If you have never attached other block volume to the instance, you can choose the first one /dev/oracleoci/oraclevdb. Otherwise, you can choose the others as long as they are not in use yet. To check if the device name is in use, simply SSH to the compute and run 'ls /dev/oracleoci' to check.
- Click 'Attach' button to proceed, and close the info pop up to start attaching block storage to instance.
- In a minute, your block volume should be attached to the instance.

### Step 3: Run iSCSI Commands in Compute

- From the screen above, look at the instance attached and click on the button with three dots (...) on the right hand side of the instance details table
- Click on 'iSCSI Commands & Information', and copy the commands for Attach
- SSH to the compute and login as user 'opc'
- Run the commands you copied, example commands (DO NOT USE THE SAMPLE) are as follows:  

sudo iscsiadm -m node -o new -T iqn.2015-12.com.oracleiaas:6f264893-59a8-4434-af7c-3a08b5877f89 -p 169.254.2.2:3260  
sudo iscsiadm -m node -o update -T iqn.2015-12.com.oracleiaas:6f264893-59a8-4434-af7c-3a08b5877f89 -n node.startup -v automatic  
sudo iscsiadm -m node -T iqn.2015-12.com.oracleiaas:6f264893-59a8-4434-af7c-3a08b5877f89 -p 169.254.2.2:3260 -l  

- Verify the result by running 'ls -l /dev/oracleoci/oraclevd*' to check if oraclevdb is already there

### Step 4: Format and Mount Block Volume to Local FS in Compute

- Format the block volume with the following command:  

sudo mkfs -t ext4 /dev/oracleoci/oraclevdb  

Enter y to proceed  

- Create a local directory of your choice, typically, we use /u01, /u02, etc.  

sudo mkdir /u01  

- Mount the file system:  

sudo mount /dev/oracleoci/oraclevdb /u01  

** Note: If after reboot, your file is missing in /u01, rerun this command to mount again.  

- You are ready to use the file system. To verify the result, run 'df -h' to see the new file systems and its available storage.



