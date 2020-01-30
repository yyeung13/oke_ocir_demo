
# Customize Default Tomcat

While there are many ways to customize a Tomcat image, here I will show example of one easy way of customizing rather than building a complex Dockerfile. The objective here is to restore default apps into Tomcat so that we have something to test with after Tomcat is deployed

The steps are as follows:

1. Create a Docker Container from Tomcat image

- Run docker command 'docker create --name tomcat -p 8080:8080 tomcat:latest'. This creates a container with the name 'tomcat'. It should display the name of container after execution, something like 'c39fc742b74ace34cb29911b7f026f754c6323826dff6dfc32e93c1c9a819b10'
- Inspect the container by running 'docker ps -a', it will display the list of containers. The container we just created should be called 'tomcat'
- Start the container with command 'docker start tomcat'
- You can now review the container by navigating to http://localhost:8080. You should see a Page Not Found (404) error as by default, nothing is deployed on Tomcat

2. Now let's modify the container to deploy all default apps for demo purposes

- You can access the running container by 'docker exec -it tomcat2 bash'. This will open up a terminal to inspect and modify the container
- In case you are new to Tomcat, all apps should be deployed under webapps folder. The default Tomcat image has no files under webapps folder. However, all default apps are in webapps.dist folder
- To deploy these apps, from '/usr/local/tomcat/webapps.dist', run this command 'cp -r . ../webapps'.
- To verify the above is success, inspect the original folder of webapps and you should see a few folders are now available inside
- Exit the container by running 'exit'
- Now you have changed the container to deploy the default webapps. You can now navigate back to http://localhost:8080 to ensure you can see default Tomcat homepage
- To commit the changes in container back to image, run 'docker commit tomcat'
- To ensure the changes have been commited, run 'docker images'. You should see a new image is now created with no name but an image ID, e.g., 5ff5e0961a5e.
- Now let's tag this docker image in preparation to push to OCIR by running 'docker tag 5ff5e0961a5e <region_code>.ocir.io/<tenancy_namespace>/<repo_name>/<image_name>', e.g., 'docker tag 5ff5e0961a5e iad.ocir.io/mytenancy/demo/tomcat2'. Note that I have purposely use a different image name so as not to overwrite the default tomcat image in OCIR. You can also choose to overwrite the default tomcat image if you want to.
- After tagging, you can now push the image to OCIR by running 'docker push <region_code>.ocir.io/<tenancy_namespace>/<repo_name>/<image_name>:latest', e.g., 'docker push iad.ocir.io/mytenancy/demo/tomcat2'

3. Upon successful push, you can use OCI CLI from Debian to pull the image and deploy to OKE

- From Debian console, update YAML file tomcat.yaml to update the image location from 'tomcat' to 'tomcat2', e.g., iad.ocir.io/mytenancy/demo/tomcat2
- If the previous Tomcat pod is still running, remove it by 'kubectl delete -f tomcat.yaml'
- Create the pods again by 'kubectl create -f tomcat.yaml'
- Inspect the pods by 'kubectl get po', you should see tomcat pod running
- Inspect the services by 'kubectl get services', you should see the load balancer service running and the public IP will be shown
- Navigate to http://<public_ip>:8080 to see the new page, this time you should not see a 404 error
