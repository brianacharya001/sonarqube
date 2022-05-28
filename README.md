Explanation of each Kubernetes config file
In order to persist changes within SonarQube, a persistent volume such as Postgres is required.

Postgres related


sonar-pv-postgres.yml
This is the Postgres persistent volume that abstracts away the persistent storage.
They have a lifecycle that is independent of the Pod and/or Deployment resources and are thus not impacted by their health.
More information can be found here Persistent Volumes


sonar-pvc-postgres.yml
Persistent Volume Claim.
This is the request for storage by a user. A PVC consumes PV resources.
More information can be found here Persistent Volume Claims


sonar-postgres-deployment.yml
This is where the container specification for the Postgres Pod resource lives.
The config file also connects the Postgres claim to the Pod resource via volume mounts.
More information can be found here Deployments


sonar-postgres-service.yml
This is where the Postgres service gets exposed (as a network service) to other resources (like the SonarQube pod resource).
More information can be found here Services



SonarQube related


sonarqube-deployment.yml
This is where the container specification for the 7.9.1-community version of SonarQube lives.
The config file states that 2GB of memory is required.
More information can be found here Deployments


sonarqube-service.yml
This is where the SonarQube service gets exposed (as a network service) to other resources.
It is with this config file that we are allowed to hit the SonarQube service from a browser.
More information can be found here Services

Install and run SonarQube locally

Install Docker Desktop and configure Kubernetes

Follow the instructions here Docker Desktop to install Docker desktop
Once Docker Desktop is installed, in your menu bar, click on the Docker icon

Open Preferences

Go to the Advanced tab
Drag the Memory slider to 4.0 GiB

Go to the Kubernetes tab
Click the checkbox Enable Kubernetes

Click Apply and wait for Docker and Kubernetes to restart


Ensure that the docker-desktop Context is set

Click on Docker icon in menu bar
Click on docker-desktop in the Kubernetes section




Install Sonar scanner

brew install sonar-scanner


Deploy your instance of SonarQube (will take a few minutes for pods to fully warm up and load SonarQube)

kubectl create secret generic postgres-pwd --from-literal=password={some made up password}
kubectl create -f sonar-pv-postgres.yml
kubectl create -f sonar-pvc-postgres.yml
kubectl create -f sonar-postgres-deployment.yml
kubectl create -f sonarqube-deployment.yml
kubectl create -f sonarqube-service.yml
kubectl create -f sonar-postgres-service.yml


Access your local SonarQube

Find the nodePort of your sonar service

kubectl get service sonar --output yaml
Copy the number next to nodePort IE: 31564



Open a new Chrome/Firefox tab/browser

Go to localhost:{nodePort you just copied}





To delete the Kubernetes deployment is as follows

kubectl delete deployment sonar-postgres
kubectl delete deployment sonarqube
