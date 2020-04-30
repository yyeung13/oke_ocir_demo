# WebLogic 14c Installation on OCI Guide

## Pre-requisite

This guide assumes you have already provisioned OEL Compute on OCI and have SSH access to the environment

## Step 1: Download WebLogic Server Binary

- Secure Shell into OCI compute and login as opc, you should not need to enter password since we are using the generated private key
- Create a new directory under opc home called 'install', this folder should be accessable from /home/opc/install. (mkdir install)  

![Screenshot 1](images/weblogic_install/screenshot1.jpg)  

- Download the generic installer for WebLogic Server 14c from https://www.oracle.com/middleware/technologies/fusionmiddleware-downloads.html, and put the file fmw_14.1.1.0.0_wls_Disk1_1of1.zip into the install folder as shown below:  

![Screenshot 2](images/weblogic_install/screenshot2.jpg)  

## Step 2: Install Java

You can check the available JDK with the command: yum list jdk*, a sample output is as follows:  

![Screenshot 3](images/weblogic_install/screenshot3.jpg)  

We will install JDK 11 here, run the command: sudo yum install jdk-11.0.7  

![Screenshot 4](images/weblogic_install/screenshot4.jpg)  

Once installation complete, you can verify the Java version by running: java -version  

You should be able to see the follow  

![Screenshot 5](images/weblogic_install/screenshot5.jpg)  


## Step 3: Export Display from OCI

The easist way is to use MobaTerm (https://mobaxterm.mobatek.net/download.html). You can, however, use other ways such as VNC too. This guide will explain how to export display with MobaTerm. The rest of the guide assume you have MobaTerm installed locally (Home edition is sufficient)  

Follow instruction in (https://docs.cloud.oracle.com/en-us/iaas/Content/Resources/Assets/whitepapers/run-graphical-apps-securely-on-oci.pdf) to export display. For simplicity, this guide will highlight the steps you need to perform here.  

The commands you need to run in sequence are:  

- sudu su -
- cd /etc/ssh/
- vi sshd_config
- Search for the line that has X11UseLocalhost yes (it’s commented out).
- remove the comment from the beginning of the line.
- Search for the line that has X11DisplayOffset (it’s commented out).
- remove the comment from the beginning of the line.
- save the file  

![Screenshot 7](images/weblogic_install/screenshot7.jpg)  

- restart SSHD: systemctl restart sshd
- Install xauth & xterm: yum -y install xauth xterm
- Exit root environment by running: exit

## Step 4: Extract WebLogic Installer

From install folder, run command: unzip fmw_14.1.1.0.0_wls_Disk1_1of1.zip  

![Screenshot 6](images/weblogic_install/screenshot6.jpg)  

Run the install by running command: java -jar fmw_14.1.1.0.0_wls.jar  


