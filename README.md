# CS454 Midterm

## Prerequisites 

- Docker
- Docker Compose
- A Linux VM (I used an Ubuntu VM and VirtualBox, but it should work with other types as well)
  - I recommend you change your port forwarding settings to match the ones shown below (for the VM).
<img width="600" height="146" alt="pt2-VM-portForwarding" src="https://github.com/user-attachments/assets/d1199c7c-183f-4b9a-9da7-d5fabed08de0" />

- kubectl
- k3d
- curl
- Node.js (22 or newer)
- npm

- Extra Note:) Before starting, ensure that your main machine and VM are able to communicate effectively


## Part 1-2:) Small Web Service & Docker Compose Inside a Linux VM (Incomplete)

First, we will build the actual image, ensure it exists, start the application, and check the current service status (make sure it is running as desired).
```bash
sudo docker build -t cs454-midterm .
sudo docker images
sudo docker compose up -d --build
sudo docker compose ps
```
^Note:) Replace "cs454-midterm" if you want a different name^

Now we will test the endpoints (health and the main one). Both were tested in the Ubuntu VM.
The counter should increase after each run.
```bash
curl http://localhost:8080/health
curl http://localhost:8080/
```
- If desired, you can use "-i" after "curl" in order to get more detailed results.

Now we will run from Windows Powershell.
```bash
curl.exe http://127.0.0.1:18080/health
curl.exe http://127.0.0.1:18080/
```


You can check the logs using the following:
```bash
sudo docker compose logs
```





## Part 3:) Kubernetes Deployment (Incomplete)

From the Linux VM (Ubuntu for me), import the image into the cluster and confirm its existence using the following: 
```bash
k3d image import cs454-midterm:latest -c practiceCluster
kubectl get nodes
```
Now we need to add/apply the configuration files:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```
Now, we likely want to check on the rollout, deployment, and pods:
```bash
kubectl rollout status deployment/cs454-midterm
kubectl get deployment cs454-midterm
kubectl get pods -o wide
```
*-------------------Placeholder Space------------------------------*

Delete a pod, then check for a replacement (should appear soon after deletion).
```bash
kubectl delete pod *insert existing pod name here*
kubectl get pods -o wide
```



## Part 4:) Controlled Troubleshooting
........................
Here, we will change the service selector to a label that doesn't match the Pods. 
We will check the Endpoint Slices and Pod Readiness before and after the label is changed to the incorrect one, and after the label is changed back. We'll make sure everything seems to be running as expected first. 
```bash
kubectl get pods -o wide
kubectl get endpointslices -l kubernetes.io/service-name=cs454-midterm
```
Now we will change the label to "somethingWrong". Afterwards, we perform tests that show the consequences of doing this.
```bash
kubectl patch service cs454-midterm -p '{"spec":{"selector":{"app":"somethingWrong"}}}'
kubectl get endpointslices
kubectl exec service-test -- curl -sS -i --max-time 5 http://cs454-midterm:8080/health
```
Since the label is wrong, we can not connect to the server and the endpoints become unusable.

Let's fix this by changing it back.
```bash
kubectl patch service cs454-midterm -p '{"spec":{"selector":{"app":"cs454-midterm"}}}'
```

## Part 5:) OpenStack Deployment Analysis
........................



