# OKE and OCIR Demo

This is a demo of Oracle Kubernetes Engine and Oracle Cloud Infrastructure Registry. Please feel free to report any issues to Yang Yang (y.yeung@oracle.com).

This demo assumes you already have Oracle Cloud Infrastructure Gen 2 account and have the necessary access to OKE, OCIR, Compute/Storage/Compartments. The following has been tested on Oracle Cloud Infrastructure Gen 2 as of 29 Jan 2020.

The overview of the demo is as follows:

1. Configure New Compartment for OKE

- Login to OCI from http://cloud.oracle.com. You will need your OCI Gen 2 tenancy name, username and password to login. Should you require a trial account, you can contact Oracle.
- Upon Successful Login, navigate from the hamburger icon on the top left hand corner to Identity -> Compartments
- Create New Compartment
  - Click on 'Create Compartment'
  - Specify the following:
    - Name: democompartment
    - Description: This is a demo compartment for OKE
    - Parent Compartment: (leave as default root compartment)
  - And click 'Create Compartment'
  - You should see the new compartment created with status 'Active'

2. Configure Policy to Allow OKE to Manage Resources in OCI

- Click on 'Policies' from the Left Menu
- If the policy 'allow_OKE' has  not been created already, create a new Policy called 'allow_OKE'
  - Click on 'Create Policy'
  - Specify the following:
    - Name: allow_OKE
    - Description: Allow OKE to manage resources
    - Version Date: Keep Policy Current
    - Select root compartment as this policy should be applicable to the root compartment instead of only the specific compartment for OKE
    - Add policy statement as follows 'allow service OKE to manage all-resources in tenancy'. Note that this **MUST** be exact.
  - Click on 'Create'
  - You should see the new policy created

3. Configure New OKE Cluster

  - From the hamburger menu, select 'Developer Services' -> Container Clusters (OKE)
  - In the left menu under 'List Scope', choose the compartment you created earlier, e.g., democompartment
  - Click on 'Create Cluster'
  Create new cluster by clicking 'Create Cluster'
  - You are presented with two options, 'Quick Create' or 'Custom Create'. Unless you are an advanced user, choose 'Quick Create' so that OKE will create all necessary resources for you
  - Select 'Quick Create' and click on 'Launch Workflow'
  - Enter the following details:
    - Name: mycluster
    - Shape: You can choose other VM shapes if you want to. For demo purpose, I recommend to choose VM Standard 2.2
    - Number of Nodes: You can adjust based on your requirement, I recommend to leave it as default 3
    - Add Ons: Enable both Kubernetes Dashboard and Tiller
  - Click on 'Next'
  - Review details and click on 'Create Cluster', this will take about 10-20 seconds to complete.
  - Click on 'Close' when the above completes
  - You can now see the status of the OKE cluster as 'CREATING'. Wait for this to complete. Once done, you have now created a new Kubernetes Cluster!
  
4. Configure Command Line Interface (CLI) for OKE
  - CLI is for you to manage the OKE cluster created. You can configure CLI on your laptop with Debian or Powershell. For this demo, I am using Debian
  - Before you can proceed, capture the following information:
    - User OCID: Click on Profile Icon from top right hand corner of OCI console, and select 'User Settings'. You can find User OCID from 'User Information' tab
    - Tenancy OCID: Click on Profile Icon from top right hand corner of OCI console, and select 'Tenancy: <tenancy_name>'. You can find Tenancy OCID from 'Tenancy Information' tab
    - Region: From Region drop down on top menu, select Manage Regions. You region identifier is shown on this screen
  - If you have not installed OCI CLI before, follow this link to install OCI CLI (https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm)
  - Setup OCI CLI by running 'oci setup config', and supply the following information:
    - Location for config: <accept default>
    - User OCID
    - Tenancy OCID
    - Region
    - Generate new API Signing Key (Enter Y)
    - Choose not to use passphrase for the private key
    - Accept default for the rest
  - You public/private OCI Signing key is now stored in <home>/.oci
  - Configure API Signing key in OCI from "User Settings' menu and click 'API Keys' under 'Resources' menu. Note that you can only specify up to 3 signing keys here
  - Click 'Add Public Key', and upload the content in oci_api_key_public.pem here. This file can be found in <home>/.oci
  - Now go back to 'Container Cluster (OKE)', click on 'mycluster'. OKE make it easy for you to complete the rest of CLI setup by providing detailed instruction under 'Access Kubeconfig', under the cluster name 'mycluster'. Click that and follow the instructions. The paramters required has been pre-filled for you. Yay!
  - Upon completion, run a quick check on the pods by running 'kubectl get po'. You should see no pods are running yet. You are ready to move on to next step
  
5. Access Kubernetes Dashboard

  - To access the GUI-based Kubernetes Dashboard, followed the detailed instruction under 'Resources' -> 'Access Kubernetes Dashboard'. Again, detailed instrunctions are provided by OKE already.
  
6. Obtain Tomcat Images
  - Now that OKE is up and running, let's move on to OCIR and see how we can use it. First, we obtain a tomcat image locally from Docker Hub (If you have not installed Docker locally, please go ahead to install it first)
  - Run 'docker pull tomcat' if you have never pulled official tomcat image before. You may be asked to login to Docker Hub if you have not login before. Should you are new to Docker, please sign up with Docker before this step
  - To verify your image is already downloaded, you can run 'docker images' and check the output for Tomcat image
  - To run the downloaded image locally first, run 'docker run -it -p 8080:8080 tomcat', you should see Tomcat starting. You can access Tomcat from http://localhost:8080. However, you should see a page not found error. This is because the default Tomcat image is a bare minimum image with no apps deployed. I'll post more details on how to deploy apps to Tomcat subsequently in this tutorial. For now, please press Ctrl+C and move on to the next step.

7. Push Tomcat Image to OCIR
  - I'll provide simple steps here on how to do this. Should you require more details, please refer to official guide here https://www.oracle.com/webfolder/technetwork/tutorials/obe/oci/registry/index.html
  - Generate Authentication Token from OCI Console
    - Go to 'User Profile' -> 'User Settings'
    - Under 'Resources' menu, click 'Auth Tokens'
    - Click on 'Generate Token' to configure a new token. Enter token name as 'mytoken' and click on 'Generate Token'. You will be shown the secret token in the next screen. Keep the token secret generated as you will not see that again
    - Note the tenancy namespace by navigation from OCI Console's hamburger icon to 'Developer Services' -> 'Registry'. The tenancy namespace will be a random string shown next to the home icon. Note this tenancy namespace too.
    - Identify your domain key from https://docs.cloud.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm. For example, if you are on us-ashburn-1, your domain key is iad
    - Before you can push an image to OCIR, you need to login first. Run 'docker login <domain_key>.ocir.io', e.g., docker login iad.ocir.io
      - Provide user name in the following format: <tenancy-namespace>/oracleidentitycloudservice/<username>, e.g., mytenancy/oracleidentitycloudservice/y.yeung@oracle.com
      - Provide the token secret you kept when generating the auth token
  - Now tag the docker image to push with 'docker tag <local_image_name>:latest <region-key>.ocir.io/<tenancy_namespace>/<repo-name>/<image-name>:<tag>', e.g., docker tag tomcat:latest iad.ocir.io/<tenancy_namespace>/demo/tomcat:latest
  - Push image by running 'docker push iad.ocir.io/<tenancy_namespace>/demo/tomcat:latest'
  - Your image is now uploaded to OCIR and you can verify from OCI Console. Navigate from hamburger icon to 'Developer Services' -> 'Registry'. Make sure your image can be found here
    
8. Pull Tomcat Image from OCIR and Deploy Pods in OKE

  - I'll provide simple steps here on how to do this. Should you require more details, please refer to official guide here https://www.oracle.com/webfolder/technetwork/tutorials/obe/oci/oke-and-registry/index.html
  - From OCI CLI command prompt, create a new file called 'createSecret.sh' with the following content:
    - kubectl create secret docker-registry ocirsecret
--docker-server=<region-key>.ocir.io --docker-username='<tenancy-namespace>/<oci-username>' --docker-password='<oci-auth-token>' --docker-email='<email-address>'
    - The name of the secret is called ocirsecret above (you can change to other name if you want to) and you will be providing the same ID and password used for OCIR login in previous steps
  - Save this file and change mode 'chmod 755 createSecret.sh'
  - Run the above file './createSecret.sh'
  - To verify the secret is created, run 'kubectl get secrets' and make sure the secret you created can be seen here
  - Create a empty yaml file called 'tomcat.yaml' with the following content
  
```
  
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
spec:
  selector:
    matchLabels:
      app: tomcat
  replicas: 1
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
    # enter the path to your image, be sure to include the correct region prefix
        image: <region_code>.ocir.io/<tenancy_namespace>/tomcat:latest
        ports:
        - containerPort: 80
      imagePullSecrets:
    # enter the name of the secret you created
      - name: ocirsecret
---
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service
spec:
  ports:
  - name: admin
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: tomcat
  type: LoadBalancer
```
   - Change the image location to match the location used for docker push in the above yaml file
   - If the OCIR secret is not named 'ocirsecret', change the secret name in the above yaml file accordingly too
   - Save the above file
   - To pull the image from OCIR, run 'kubectl create -f tomcat.yaml', OKE will now pull image from OCIR and deploy it
   - To verify deployment, use 'kubectl get po' to check if the Tomcat pod is running
   - To check how to access Tomcat from public IP, use 'kubectl get services' to check the public IP for the tomcat load balancer. Note that sometimes it does take a bit longer to assign public IP to the load balancer service
   - Test Tomat with http://<public_ip>:8080, you should see a page not found (404) error as explained earlier
   - Great, you are done!
   - [Optional] To scale up/down the Tomcat pod, simply change the value of 'replica' in tomcat.yaml, and run 'kubectl apply -f tomcat.yaml'
   - [Optional] Should you wish to housekeep whatever we created in this tutorial, simply run 'kubectl delete -f tomcat.yaml'
    
    
9. [Optional] Configure Private GIT in Developer Cloud Service

