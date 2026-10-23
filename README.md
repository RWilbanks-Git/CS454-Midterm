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


## Part 1-2:) Small Web Service & Docker Compose Inside a Linux VM (Incomplete)

First, we will build the actual image, ensure it exists, start the application, and check the current service status (to make sure it is running correctly).
```bash
sudo docker build -t cs454-midterm .
sudo docker images
sudo docker compose up -d --build
sudo docker compose ps
```

Now we will test the endpoints (health and the main one). Both were tested in the Ubuntu VM.
The counter should increase after each run.
```bash
curl -s http://localhost:8080/health
curl -s http://localhost:8080/
```
Now we will restart and observe what happens to the counter ("requestCount" should reset)
```bash
sudo docker compose restart api
sudo docker compose ps
curl http://localhost:8080/
```
<img width="922" height="407" alt="pt2-before-afterRestart" src="https://github.com/user-attachments/assets/dd0c3114-616c-4d61-83d5-cfc0eff68671" />


Now we will run from Windows Powershell.
```bash
curl.exe http://127.0.0.1:18080/health
curl.exe http://127.0.0.1:18080/
```
<img width="950" height="231" alt="pt2-windowsRequests" src="https://github.com/user-attachments/assets/5a6c6ea5-e647-4be4-af95-46a6172f3678" />


Check the logs using the following:
```bash
sudo docker compose logs api
```
<img width="617" height="452" alt="pt2-logs" src="https://github.com/user-attachments/assets/0ef6bc7c-8935-4bf1-a72c-5f15b56746b9" />



## Part 3:) Kubernetes Deployment (Incomplete)

From the Linux VM (Ubuntu for me), import the image into the cluster and confirm its existence using the following: 
```bash
k3d image import cs454-midterm:latest -c practiceCluster
kubectl get nodes
```
<img width="615" height="117" alt="pt3-1 clusterReady" src="https://github.com/user-attachments/assets/7b96578a-7fd4-4e19-a76c-4084ed8dd87b" />


Add/apply the configuration files:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```


Now, check on the rollout, deployment, and pods:
```bash
kubectl rollout status deployment/cs454-midterm
kubectl get deployment cs454-midterm
kubectl get pods -o wide
```
<img width="936" height="333" alt="pt3-3 kubernetesResources" src="https://github.com/user-attachments/assets/e9832e3c-793b-4784-b746-4c954ac98f5f" />
<img width="771" height="146" alt="pt3-deploymentReady" src="https://github.com/user-attachments/assets/5b6d6726-3da7-4af1-8014-aed91e62c3a1" />

Delete a pod, then check for a replacement (should appear soon after deletion).
```bash
kubectl delete pod *insert existing pod name here*
kubectl get pods -o wide
```
<img width="842" height="331" alt="pt3-4 podKilling" src="https://github.com/user-attachments/assets/3bf11a0f-5c88-46f8-9d48-5d0bd30f6413" />
<img width="835" height="311" alt="pt3-5 newPod" src="https://github.com/user-attachments/assets/fd32188e-a70b-4f5a-adf7-fc7f538cd737" />

## Part 4:) Controlled Troubleshooting
Here, we will change the service selector to a label that doesn't match the Pods. 
We will check the Endpoint Slices and Pod Readiness before and after the label is changed to the incorrect one, and after the label is changed back. 
We'll make sure everything seems to be running as expected first. 
```bash
kubectl get pods -o wide
kubectl get endpointslices -l kubernetes.io/service-name=cs454-midterm
```
<img width="1245" height="271" alt="pt4-preChange" src="https://github.com/user-attachments/assets/0074a287-1840-483a-9c86-174ff8fd935f" />


Now we change the label to "somethingWrong". Afterwards, we perform tests that show the consequences of doing this.
```bash
kubectl patch service cs454-midterm -p '{"spec":{"selector":{"app":"somethingWrong"}}}'
kubectl get endpointslices
kubectl exec service-test -- curl -sS -i --max-time 5 http://cs454-midterm:8080/health
```
Since the label is wrong, we can not connect to the server and the endpoints become unusable.
<img width="866" height="223" alt="pt4-postChangeFails" src="https://github.com/user-attachments/assets/85d8ee59-d1e8-4637-815f-53adc8e5a972" />


Let's fix this by changing it back.
```bash
kubectl patch service cs454-midterm -p '{"spec":{"selector":{"app":"cs454-midterm"}}}'
```
<img width="860" height="290" alt="pt4-postFixTests" src="https://github.com/user-attachments/assets/a492e484-4908-4cb3-9485-9e0299bb4b82" />


## Part 5:) OpenStack Deployment Analysis
........................



