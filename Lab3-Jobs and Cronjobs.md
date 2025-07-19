## Jobs and Cronjobs in Kubernetes

#### JOB: 
A Job creates one or more Pods and will continue to retry execution of the Pods until a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created. Suspending a Job will delete its active Pods until the Job is resumed again.

A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted (for example due to a node hardware failure or a node reboot).

You can also use a Job to run multiple Pods in parallel.

#### CRONJOB
A CronJob creates Jobs on a repeating schedule.

CronJob is meant for performing regular scheduled actions such as backups, report generation, and so on. One CronJob object is like one line of a crontab (cron table) file on a Unix system. It runs a Job periodically on a given schedule, written in Cron format.

### Task 1: Jobs in Kubernetes 
```
vi job-pod.yaml
```
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: jobs-hello
spec:
  template:
    metadata:
      name: jobs-pod
    spec:
      containers:
      - name: jobs-ctr
        image: busybox
        args:
        - /bin/sh
        - -c
        - "echo HELLO WORLD !!!!!"
      restartPolicy: Never
```
```
kubectl apply -f job-pod.yaml
```
```
kubectl get jobs
```
```
kubectl get pods
```

Read logs 
```
kubectl logs <name of jobpod>
```

Describe the job
```
kubectl describe jobs jobs-hello
```
```
kubectl describe pod <pod-name>
```
Delete the job
```
kubectl delete -f job-pod.yaml
```

### Task 2: Cronjobs 

Create a yaml called cron.yaml. Use the content given below to fill the file
```
vi cronjob.yaml
```
```yaml
 
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob-hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cronjob-ctr
            image: busybox
            args:
            - /bin/sh
            - -c
            - "echo Hello World!"
          restartPolicy: OnFailure
```

```
kubectl get po
```
```
kubectl get job
```
```
kubectl get cronjobs 
```

```
kubectl apply -f cronjob.yaml
```
```
kubectl get cronjobs.batch
```
```
kubectl get pod
```
```
kubectl logs <cronjob-pod-name>
```
In new tabs , you can add a watch to job and pods by the below command
```
kubectl get po -w
```
```
kubectl get jobs -w
```
To check the number of executions of the cronjobs
```
kubectl describe cronjobs
```
To edit the running cronjob
```
kubectl edit cronjob <cronjob-name>
```
To generate CronJob schedule expressions, you can also use web tools like https://crontab.guru/
