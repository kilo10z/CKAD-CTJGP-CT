## Deployment Strategy

### Task 1: Recreate Strategy in Kubernetes 
```
vi recreate.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep2
  name: dep2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dep2
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: dep2
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```
```
kubectl apply -f recreate.yaml
```
Add a watch on the pods in a new tab
```
kubectl get po -w
```
Set a new image for the deployment
```
kubectl set image deploy dep2 nginx=nginx:latest --record
```
Check how the pods are getting deleted and recreated. 
![image](https://github.com/user-attachments/assets/c59a35cd-0e63-44b9-8edc-6265816e53b7)

Check the rollout history
```
kubectl rollout history deployment dep2
```
![image](https://github.com/user-attachments/assets/3c430837-2857-405b-b50d-c6435caeb5ae)

Cross check if the image has been updated by executing the below command
```
kubectl describe deployments.apps dep2
```
Now lets rollback. In the below command  if no revison number is given, it rollbacks to the immediate previous one.
```
kubectl rollout undo deployment dep2 --to-revision 1           
```
Check the history
```
kubectl rollout history deployment dep2
```
![image](https://github.com/user-attachments/assets/09fc3241-efe9-40f7-b6ee-bfc65c4aad30)



### Task 2: Rolling Update in Kubernetes 

```
vi dep.yaml 
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep1
  name: dep1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: dep1
  strategy: {}
  template:
    metadata:
      labels:
        app: dep1
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```

Note the strategy.Since nothing is given the default strategy comes into place which is the rolling update.

Apply the yaml file
```
kubectl apply -f dep.yaml
```
```
kubectl get deployments.apps,pod,rs
```
![image](https://github.com/user-attachments/assets/9435e108-84c1-4ff9-bb0d-c943a050eee9)

Note the Statgey and events by describing the deployment
```
kubectl describe deployments.apps dep1
```
Add a watch on the pods in a new tab
```
kubectl get po -w
```
![image](https://github.com/user-attachments/assets/f0a882bb-ec41-4e1e-ab7a-485502e2b062)

Set a new image for the deployment
```
kubectl set image deploy dep1 nginx=nginx:latest --record
```
Check how the pods are getting deleted and recreated. 

![image](https://github.com/user-attachments/assets/ca240c77-2b1e-44c8-acdc-d7a947f397f7)

Cross check if the image has been updated by executing the below command
```
kubectl describe deployments.apps dep1
```
To check the rollout history. The below command shows history of 10 versions.
```
kubectl rollout history deploy dep1
```
![image](https://github.com/user-attachments/assets/7fcb6525-e111-4b87-8bcd-401de335016e)

To Rollback
```
kubectl rollout undo deploy dep1 --to-revision 1
```
To check the history of a particular revision
```
kubectl rollout history deploy dep1 --revision=<revision-number>
```
![image](https://github.com/user-attachments/assets/88e8c221-53a5-47a2-8828-672c2c2de5ee)



### Task 3: Blue/Green Deployment in Kubernetes 
```
vi web-blue.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-blue
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web-blue
    spec:
      containers:
      - image: mandarct/web-blue:v1
        name: web-blue
        ports:
        - containerPort: 80
          protocol: TCP
```
```		 
kubectl apply -f web-blue.yaml
```
```
kubectl get deploy,po
```
![image](https://github.com/user-attachments/assets/b446a369-5fed-4672-ae2a-7212ccbed87b)


Now create NodePort service to access application
```		 
vi svc-web.yaml
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-web
spec:
  ports:
  - port: 80        # Service port(internal)
    protocol: TCP
    targetPort: 80  # Container port
    nodePort: 32123 # Range from 30000 to 32767 . This is the external port number
  selector:
    app: web-blue
  type: NodePort

```
```	 
kubectl apply -f svc-web.yaml
```
```
kubectl get svc,ep
```
![image](https://github.com/user-attachments/assets/b3dd924e-7d2c-451b-9085-e084717e305b)

Access you application

![image](https://github.com/user-attachments/assets/f85d13a8-788f-4454-8b29-4e61a9ab9225)

Create another deployment (Green)
```
vi web-green.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-green
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web-green
    spec:
      containers:
      - image: mandarct/web-green:v1
        name: web-green
        ports:
        - containerPort: 80
          protocol: TCP
```
```
kubectl apply -f web-green.yaml
```
```
kubectl get deployment,po
```
![image](https://github.com/user-attachments/assets/4d5ed7ed-52c5-4324-9cac-8dfb8dc8c9c7)


Access this application using same service that we created previously, by changing the selector in the Service yaml file.

Replace Selector `web-blue` by `web-green`
```	 
kubectl replace -f svc-web.yaml --force
```
![image](https://github.com/user-attachments/assets/abaa30b2-e93d-4b87-9479-210cd6fe4a5a)


Access you application on the port 32123

![image](https://github.com/user-attachments/assets/964d4e6a-4a13-43bd-a620-9115d58d935b)


### Task 4: Canary Deployment in Kubernetes 

Service and deployment should have a common label.
Replace labels and selectors with `type: web-app` in the yaml file of service and both the deployments and replace all

```
kubectl replace -f web-green.yaml --force
```
```
kubectl replace -f web-blue.yaml --force
```
```
kubectl replace -f svc-web.yaml --force
```

Check all obejcts
```
kubectl get po,svc,ep
```
![image](https://github.com/user-attachments/assets/146ed1d4-04c0-4cfc-8ca4-eaa56c123369)

Describe the endpoint. You would notice all 6 pods Ip added to the end-point
```
kubectl describe ep svc-web
```
![image](https://github.com/user-attachments/assets/076f31be-99ac-4657-a515-2901c3cbfc6a)

Access the application on port 32123. Refresh the page a few times. Sometime it will show Blue, other time it will show green. 
![image](https://github.com/user-attachments/assets/680d2a0d-7bb6-4db1-b8b3-a4b465000f0e)
![image](https://github.com/user-attachments/assets/ac164c9b-3f83-47f1-9342-68126db7149a)






