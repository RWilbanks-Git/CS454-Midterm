# CS454 Midterm

## Prerequisites 

- Docker
- Docker Compose
- A Linux VM (I used Ubuntu, but it should work with other types as well)
  - Make sure the network is set to "NAT", as we want to be able to communicate with the main machine.
- kubectl
- k3d
- curl
- Node.js (22 or newer)
- npm

- Extra Note:) Before starting, ensure that your main machine and VM are able to communicate effectively


## Part 1-2:) Small Web Service & Docker Compose Inside a Linux VM (Incomplete)

First, we will build the actual image, ensure it exists, start the application, and check the current service status (make sure it is running as desired).
```bash
sudo docker build -t cs454-midterm:latest .
sudo docker images
sudo docker compose up -d --build
sudo docker compose ps
```
^Note:) Replace "cs454-midterm:latest" if you want a different name^

Now we will test the endpoints. Both were tested in the Ubuntu VM.
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

Delete a pod, then check for a replacement (should appear soon after deletion)
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






## Endpoints
- GET /: JSON service, message, hostname, and requestCount.
- GET /health: HTTP 200 with {"status":"ok"}.
- Other routes/methods: HTTP 404.

The counter counts successful GET / requests only. Health checks do not affect it. Each process has its own counter; a new process starts with zero and its first GET / returns 1. Hostname identifies the serving container/Pod in the deployment. Do not add Redis or a database.

## Your implementation work
1. Write a Dockerfile and .dockerignore to package this service. Bind the application on its configured port; the source already listens on 0.0.0.0.
2. Write Compose configuration for one app service with runtime variables and a published port. Run Compose inside your Linux VM and document host access.
3. Write a Kubernetes Deployment with two replicas and a Service. Use the same image as Compose and document how your cluster obtains it.
4. Observe Pod replacement, introduce and fix the Service-selector mismatch, and capture evidence.
5. Complete the OpenStack analysis and the report/README required by the handout.
6. CS554: add resource requests/limits and an HTTP readiness probe, then perform the readiness experiment.

The planning files below contain prompts only and are not executable deployment configurations. Replace them with your own Dockerfile, compose.yaml, and Kubernetes manifests. No completed deployment solution is supplied.

## Kubernetes observations
A Service port-forward can select one Pod and remain pinned to it. It does not demonstrate balanced traffic or automatically switch to other ready replicas. For the selector failure, establish a new access session after the change. For the graduate readiness experiment, use requests from inside the cluster to the Service address or another access method that exercises ready Service backends.

Choose a reproducible method that makes only one replica fail its configured readiness check while its application remains running. Record Pod readiness and EndpointSlices, then restore the original configuration. The experiment method is part of your graduate work.

## Replace this README for submission
Add actual VM/networking details, image build/tag commands, Compose start/access/log/cleanup commands, cluster type/image-loading method, Kubernetes apply/access/inspection/cleanup commands, and your observations. The supplied smoke script alone is not sufficient project evidence.
