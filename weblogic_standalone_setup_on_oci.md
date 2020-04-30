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


## Step 3: 
