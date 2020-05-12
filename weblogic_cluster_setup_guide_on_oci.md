# WebLogic Cluster Setup Guide on OCI

This guide leverages on OCI features to setup a on-prem WebLogic environment on OCI (Oracle Cloud Infrastructure). This is typically applicable if you are using Oracle cloud for development, functional testing or performance testing of on-prem WebLogic applications. This guide assumes you have basic WebLogic Administration knowledge and is familiar with WebLogic Console.

## Overview

In the following steps, you will be setting up a weblogic cluster that spans across two OCI Compute. The steps you will be performing includes:

- Provision first VM (weblogic1) on OCI using standard images  
- Install WebLogic Binary on the first VM  
- Configure a new clustered WebLogic Domain with 2 managed servers  
- Configure node manager for the first VM  
- Clone the boot volume of the first VM to be the boot volume of the second VM, and provision second VM (weblogic2) using the boot volume cloned  
- Prepare the second VM to join WebLogic Cluster  



## Step 1: Provision first VM weblogic1 on OCI using standard images

Before provisioning VM, you need to generate a RSA key pair for the compute so that later on you can SSH to the compute with the key pair. You can refer to here https://www.oracle.com/webfolder/technetwork/tutorials/obe/cloud/javaservice/JCS/JCS_SSH/create_sshkey.html.

The rest of the guide assume you have generated a key pair named: id_rsa, with its private key in id_rsa.ppk and public key in id_rsa.pub.  

Now login to your Oracle cloud tenancy from http://cloud.oracle.com with your tenancy ID, username and password.  

Click on the Menu on the top left hand corner and select 'Compute' -> 'Instances'. (Note that it's a good practice to create separate compartment and VCN for multitenancy purpose, this part is not included in the guide here)  

![cluster1](images/weblogic_install/cluster1.jpg)  

Click on 'Create Instance' below:  

![cluster2](images/weblogic_install/cluster2.jpg)  

Name the new compute instance as 'weblogic1' and leave the default image as Oracle Linux:  

![cluster3](images/weblogic_install/cluster3.jpg)  

Scroll down to change the Shape from default to VM.Standard 2.4 with 4 OCPU for optimal performance for development environment. For performance testing, choose a shape that matches the production environment. Click on 'Change Shape':

![cluster4](images/weblogic_install/cluster4.jpg)  

Choose VM.Standard2.4 as shown and click 'Select Shape':  

![cluster5](images/weblogic_install/cluster5.jpg)  

Validate the network setting and ensure that you are on a public subnet as highlighted. This is to ensure a public IP will be generated for the VM so that you can SSH directly later. If you wish to use private subnet instead, note that you need to set up additional Bastion host in order to access the VM later, which is not covered in this guide.  

![cluster6](images/weblogic_install/cluster6.jpg)  

Scroll down to ensure the VM will be assigned a public IP as follows:  

![cluster7](images/weblogic_install/cluster7.jpg)  

Scroll down to specify the public key that you generated earlier. Ensure the public key is loaded as shown, and click on 'Create' to provision the VM.  

![cluster8](images/weblogic_install/cluster8.jpg)  

Wait for VM provision to complete, and review the VM details, capture the public IP of the VM for SSH access.  (I have mask the actual IP, you should get this on your VM details screen)  

![cluster9](images/weblogic_install/cluster9.jpg)  

A lot of customer out there is using putty, while the rest of the demo will be using MobaTerm for its ease of X11 Display, I have included option steps here to setup access via Putty in case you are using Putty.  

To test the VM, open Putty and enter the IP of the VM:  

![cluster10](images/weblogic_install/cluster10.jpg)  

From Putty Menu on the left, navigate to 'Connection' -> 'SSH' -> 'Auth', and specify the private key you generated earlier as shown, and click 'Open':

![cluster11](images/weblogic_install/cluster11.jpg)  

Click on 'Yes':  

![cluster12](images/weblogic_install/cluster12.jpg)  

![cluster13](images/weblogic_install/cluster13.jpg)  

That's all you need with Putty.  

With MobaTerm, the steps are very similar and the SSH private key configuration is here instead:  

![cluster15](images/weblogic_install/cluster15.jpg)  


## Step 2: Install WebLogic Binary on the first VM

Follow installation guide [here](weblogic_standalone_setup_on_oci.md)

## Step 3: Configure Cluster WebLogic Domain

### Run Domain Configuration Wizard

Start Domain Configuration Wizard from SSH from /home/opc/Oracle/Middleware/Oracle_Home/oracle_common/common/bin by running './config.sh':  

![cluster16](images/weblogic_install/cluster16.jpg)  

Select create new domain and change the default domain name to your preferred domain name. In this example, we are using 'clusterDomain' as shown. Click 'Next' to proceed:  

![cluster17](images/weblogic_install/cluster17.jpg)  

Click 'Next' to proceed:

![cluster18](images/weblogic_install/cluster18.jpg)  

Enter admin user name, password and click 'Next':  

![cluster19](images/weblogic_install/cluster19.jpg)  

For optimal performance, choose 'Production' mode and default JDK, click 'Next':  

![cluster20](images/weblogic_install/cluster20.jpg)  

Click on the checkbox next to 'Topology' in order to configure cluster, and click 'Next':  

![cluster21](images/weblogic_install/cluster21.jpg)  

Click 'Add' button twice to create two managed server, and configure its names as ms1 and ms2 respectively. In ms1, use the dropdown to pick the local IP within OCI as listen address, while for ms2, manually enter a similar IP temporary as we don't have the new IP yet for second compute. Set port number for both server as 7003, and click 'Next':  

![cluster22](images/weblogic_install/cluster22.jpg)  

Click 'Add' button to create a new cluster and enter name as 'demoCluster', and click 'Next':  

![cluster23](images/weblogic_install/cluster23.jpg)  

Click 'Next':  

![cluster24](images/weblogic_install/cluster24.jpg)  

Click 'Next' as we are not using dynamic cluster for this setup:  

![cluster25](images/weblogic_install/cluster25.jpg)  

Click on 'ms1'/'ms2' on the left and select 'demoCluster' on the right, the right arrow button will be enabled. Click on the right arrow to add both ms1 and ms2 into the cluster as shown, and click 'Next':  

![cluster26](images/weblogic_install/cluster26.jpg)  

Click on "Unix Machine" and use the 'Add' button to add two machines as shown, and click 'Next':  

![cluster27](images/weblogic_install/cluster27.jpg)  

Use the arrow to move the servers from left to right under machine1 and machine2 as shown:  

![cluster28](images/weblogic_install/cluster28.jpg)  

In summary screen, click 'Create' to proceed with domain creation:  

![cluster29](images/weblogic_install/cluster29.jpg)  

Upon completion, click 'Next' to proceed:  

![cluster30](images/weblogic_install/cluster30.jpg)  

Click 'Finish' to exit the wizard.  

![cluster31](images/weblogic_install/cluster31.jpg)  

### Open Firewall for Port 7001

In order to access the admin console remotely, we need to open up firewall for port 7001 (admin server). Should you need to access the managed servers too, you need to use the same procedure to open port 7003.  

From SSH, run this:  

- sudo firewall-cmd --permanent --zone=public --add-port=7001/tcp  
- sudo firewall-cmd --reload  

![cluster32](images/weblogic_install/cluster32.jpg)  

From OCI Cloud Console showing details of the compute weblogic1, click on the subnet name as shown:  

![cluster33](images/weblogic_install/cluster33.jpg)  

In the subnet details page, scroll down and click on the name of the default security list as shown:  

![cluster34](images/weblogic_install/cluster34.jpg)  

Click on 'Add Ingress Rule':  

![cluster35](images/weblogic_install/cluster35.jpg)  

Enter source CIDR as '0.0.0.0/0' and destination port as '7001', and click 'Add Ingress Rules'. This will add rule in the subnet to allow public Internet to have access to port 7001 to all servers within the subnet.  

![cluster36](images/weblogic_install/cluster36.jpg)  

### Start WebLogic Server

- From SSH, change working directory to domain home: cd /home/opc/Oracle/Middleware/Oracle_Home/user_projects/domains/clusterDomain  
- Create a defaut login credential by creating a directory structure from domain home:  
mkdir servers  
cd servers  
mkdir AdminServer  
cd AdminServer  
mkdir security  
cd security

![cluster37](images/weblogic_install/cluster37.jpg)  

- Create a new file boot.properties by running: vi boot.properties  

File content should be as follows:  

username=weblogic  
password=\<selected password during domain configuration wizard\>  

- Change working directory to <Domain_home>/bin  
- Start Admin server with the command: nohup ./startWebLogic.sh > admin.log &  

![cluster38](images/weblogic_install/cluster38.jpg)  


- To track the progress, run: tail -f admin.log and wait till you see this message below, this indicate WebLogic Admin Server is up:  

![cluster39](images/weblogic_install/cluster39.jpg)  

- To access the admin server from browser, open browser with the URL: http://<public_ip>:7001/console  

![cluster40](images/weblogic_install/cluster40.jpg)  

This shows WebLogic Server is now up and running.  

To start the first managed server, follow the similar steps above to create the same boot.properties file under <domain_home>/servers/ms1/security, and run the start script from <domain_home>/bin: nohup ./startManagedWebLogic ms1 localhost:7001 > server1.log &  

Monitor the log server1.log to make sure ms1 is up. You can see this from WebLogic Console:  

![cluster41](images/weblogic_install/cluster41.jpg)  

## Step 4: Configure Node Manager for First VM  

To start node manager, change working directory to <domain_home>/bin and run this: nohup ./startNodeManager.sh &  

To verify the node manager is working, navigate from WebLogic Console: <domain_name> -> Environment -> Machines, and click on Machine1:  
![cluster42](images/weblogic_install/cluster42.jpg)  

Click 'Monitoring' tab above and verify the machine is reachable.  

![cluster43](images/weblogic_install/cluster43.jpg)  

With node manager working, you can also stop/start managed server ms1 from WebLogic Console directly. We are done with the first VM and can now move on to the next step.

## Step 5: Clone Boot Volume of First VM into Second VM

We need to shutdown the first VM before cloning, go to the details screen of the first VM from Cloud Console and click on 'Stop' to shutdown the VM:  

![cluster50](images/weblogic_install/cluster50.jpg)  

Click on 'Stop Instance' to confirm:  

![cluster51](images/weblogic_install/cluster51.jpg)  

Once the instance is down, click on 'Boot volume' from the left menu:  

![cluster52](images/weblogic_install/cluster52.jpg)  

Click on the link to access boot volume as follows:  

![cluster53](images/weblogic_install/cluster53.jpg)  

From the boot volume details screen, click on 'Boot Volume Clones' and click 'Create Clone':  

![cluster54](images/weblogic_install/cluster54.jpg)  

Enter name 'weblogic2' and click on 'Create Clone':  

![cluster55](images/weblogic_install/cluster55.jpg)  

Wait for the clone to be available, and select 'Create Instance' from the right menu as follows:  

![cluster56](images/weblogic_install/cluster56.jpg)  

Enter name as 'weblogic2' as follows:  

![cluster57](images/weblogic_install/cluster57.jpg)  

For subsequent fields, follow the same setting as weblogic1 above. Proceed to provision weblogic2 by clicking 'Create'. Wait for instance provisioning to complete.  

![cluster58](images/weblogic_install/cluster58.jpg)  

Take note of the public IP and internal IP of the new VM weblogic2.  

Go back to Instances details for weblogic1 and start up weblogic1 too.  

![cluster59](images/weblogic_install/cluster59.jpg)  

## Step 6: Prepare Second VM to join WebLogic Cluster

SSH back to the first VM weblogic1 and start Admin Server & Node Manager again using same instruction earlier in this guide.  

SSH to the second VM weblogic2 and change working directory to <domain_home>.  

Open the file nodemanager.properties under <domain_home>/nodemanager, and change ListenAddress to the internal IP of weblogic2:  

![cluster60](images/weblogic_install/cluster60.jpg)  

Open local firewall in weblogic2 by running:  

- sudo firewall-cmd --permanent --zone=public --add-port=5556/tcp  
- sudo firewall-cmd --reload  

Start node manager following same instruction in first VM weblogic1 above.  


Login to the weblogic console again (http://\<public_ip_of_weblogic1\>:7001/console) again and nagivate to Machines list:  

![cluster61](images/weblogic_install/cluster61.jpg)  

Click on 'machine2' -> 'Node Manager' to update the IP of machine 2 with actual internal IP of weblogic2:  

![cluster62](images/weblogic_install/cluster62.jpg)  

Disable host name verification for all managed servers by clicking on each managed server name on the left menu and click on 'SSL' tab, 'Advanced' and select Hostname Verification as 'None'. This is becasuse the auto generated certificate is for weblogic1 and weblogic2 is using the same certificate from the same boot volume and doesn't match the name on certificate as weblogic1.  

![cluster63](images/weblogic_install/cluster63.jpg)  

Restart the admin server by shutting it down from WebLogic Console, and starting it from SSH to weblogic1. Instruction on starting admin server is in earlier part of this guide.  

By default the node manager in weblogic2 is already running. However, we have to disable the hostname verification too. SSH to weblogic2 and login as opc. Use the following command to identify the process ID of node manager: ps -eaf | grep java  

![cluster64](images/weblogic_install/cluster64.jpg)  

Kill the Node Manager with: kill -9 <pid>, e.g., if the process ID is 6641, the command to kill is: kill -9 6641  

Change working directory to <domain_home>/bin and edit the file startNodeManager.sh. Add '-Dweblogic.nodemanager.sslHostNameVerificationEnabled=false' to JAVA Options as follows:  

![cluster65](images/weblogic_install/cluster65.jpg)  

Restart node manager by running: nohup ./startNodeManager.sh > nm.log &  

Go back to WebLogic Console, click on 'Machines' -> machine1/machine2 -> 'Monitoring', and ensure both machine is reachable:  

![cluster66](images/weblogic_install/cluster66.jpg)  

WebLogic Cluster is now ready for use.



