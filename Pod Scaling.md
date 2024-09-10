## Pod Scaling

### Task 1: Manual Scaling
Create the deployment Yaml File
```
 kubectl create deployment dep1 --image nginx --replicas 3 --dry-run=client -o yaml > dep1.yaml
```
Apply the yaml file
```
 kubectl apply -f dep1.yaml
```
Check the obejcts create. You would notice Deployemnt, Replicaset and Pods getting created
```
 kubectl get all
```
![image](https://github.com/user-attachments/assets/99c66d04-e948-475e-a17d-7218d92e978c)

Describe the deployment
```
 kubectl describe deploy dep1
```
Manually scale out the deployment using the imperative method
```
 kubectl scale deployment dep1 --replicas 5
```
```
 kubectl get all
```
![image](https://github.com/user-attachments/assets/d0f79e9e-9b82-445d-a4ca-8030986057b0)

Now we will scale the deployment by editing the yaml file and increasing the replicas to 6
```
 vi dep1.yaml
```
```
 kubectl apply -f dep1.yaml
```
```
 kubectl get all
```
![image](https://github.com/user-attachments/assets/2c806739-51be-4009-8bc7-96535caf65ae)

To decrease the numebr of repliacs, we again can do it by the imperative method or by editing the yaml file.
```
 kubectl scale deployment dep1 --replicas 3
```
```
 kubectl get all
```
![image](https://github.com/user-attachments/assets/a05544cf-e63c-4ee2-ab28-524bd706a266)


### Task 2: Horizantal Pod Autoscaler(HPA)
Now lets set up HPA
```
 kubectl autoscale deploy dep1 --min 4 --max 6 --cpu-percent 70
```
```
 kubectl get hpa
```
![image](https://github.com/user-attachments/assets/d239c5e9-a27e-4f3f-908f-d9a41ce0f456)
