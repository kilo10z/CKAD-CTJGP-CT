## Helm

### Task 1:  Commonly used Helm Commands

To check the version of Helm installed
```
helm version
```
![image](https://github.com/user-attachments/assets/5046f1d4-9852-4bd3-ad7a-29657fe898c9)


To add a Helm chart repository to your local environment. Below we are adding two Helm Chart Repositories.
```
helm repo add bitnami https://charts.bitnami.com/bitnami 
```
```
helm repo add stable-charts https://charts.helm.sh/stable
```
To list the Helm chart repositories configured in your local environment.
```
helm repo list
```
![image](https://github.com/user-attachments/assets/ef243759-863c-4977-9bb3-0eea3da0b665)


To search for Helm charts related to WordPress in the configured Helm repositories
```
helm search repo wordpress
```
![image](https://github.com/user-attachments/assets/e264bd08-316b-46dc-83f0-725ac4b03960)

To install the WordPress application using the Bitnami Helm chart with the release name my-wordpress
```
helm install my-wordpress bitnami/wordpress
```
![image](https://github.com/user-attachments/assets/c53bf031-dbcf-4e41-b864-070e665f745a)

To list all the releases currently installed on your Kubernetes cluster
```
helm list
```
![image](https://github.com/user-attachments/assets/5f38ada0-d113-4172-9c34-7df08b08005b)

You can install multiple helm charts from the same repo, but with unique name
```
![image](https://github.com/user-attachments/assets/4292aae2-bfc9-4327-97f9-70a7db03564a)

![image](https://github.com/user-attachments/assets/c018071f-d7c5-4fb6-8f03-0be1f024a9cb)


To uninstall the WordPress application deployed using Helm with the release name my-wordpress
```
helm uninstall app-wordpress
```
![image](https://github.com/user-attachments/assets/c4a2e037-7e7b-49d9-90dd-9a879af404fc)

To upgrade(update) an existing chart
```
helm upgrade my-wordpress bitnami/wordpress
```
Notice the revision number

![image](https://github.com/user-attachments/assets/b9c7ff52-174f-49cd-8894-3eaa5722fdf4)

![image](https://github.com/user-attachments/assets/cdfad5e3-7732-4516-bd04-0fac913e48ec)


To download the Bitnami WordPress Helm chart to your local machine.
```
helm fetch bitnami/wordpress
```
To remove a Helm repository from your local Helm client configuration
```
helm repo remove stable-charts
```
```
helm repo remove bitnami
```
To remove Helm package manager
```
sudo apt-get remove helm
```

### Task 2:  Installing WordPress with Helm

#### Helm setup

Download the shell script for the installation of the Helm package manager and run it.
```
wget  https://s3.ap-south-1.amazonaws.com/files.cloudthat.training/devops/kubernetes-essentials/helm.sh
```
```
bash helm.sh
```
```
helm version
```

#### Setup WordPress
#Add bitnami repository to helm which contains WordPress chart.
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
```
helm repo update
```
```
helm repo list
```

Search for WordPress package from the repository and install the WordPress chart with release name wordpress-chart. You can name it as you want.
```
helm search repo Wordpress
```
Helm Charts can be directly installed from the repo, or the yaml file downloaded, extracted and then chart installed
```
helm install wordpress-chart bitnami/wordpress
```
OR Perform helm fetch to get the WordPress compressed file to the working directory. Perform ls and verify that a compressed WordPress file is present in PWD.
```
helm fetch bitnami/wordpress
```
```
ls
``` 
Extract the compressed file using the below command.
```
tar -xvzf wordpress-19.2.6.tgz
```
Now, deploy the WordPress using helm install command.  This will set up the pods and services for WordPress.
```
helm install wordpress --generate-name   ## OR helm install wordpress wordpress
``` 
#### Verify that WordPress has been set up 
Verify that the pods are running.
```
kubectl get pods
```
Now Notice that MariaDB (database) pods are part of statefulset.
```
kubectl get sts
```
```
kubectl describe sts
```
Also Notice that the front end of wordpress are part of deployment
```
kubectl get deploy
```
View the services using the below commands. Make note of the EXTERNAL IP of the LoadBalancer service.
```
kubectl get svc
```
```
kubectl get svc --namespace default -w wordpress-1637763307
``` 
Open the browser and paste the service endpoint noted on the previous step. Observe that the WordPress site is up and running.

#### Cleanup
List the current helm release and delete it
```
helm ls
```
```
helm delete <helm_Release_Name >
```
```
helm ls
```
 
 
