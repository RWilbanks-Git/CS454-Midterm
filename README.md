# CS454 Midterm

## Prerequisites 
........................
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


## Part 1:) Small Web Service
........................
First, we will build the actual image using the command below (make sure to go to the folder/directory where the Dockerfile is present before running). We will ensure it exists after. 
```bash
sudo docker build -t cs454-midterm:1.0 .
sudo docker images cs454-midterm
```
^Note:) Replace "cs454-midterm:1.0" if you want a different name^




In another terminal:
```bash
bash test/smoke.sh
```
Defaults: PORT=8080, SERVICE_NAME=cs454-midterm, MESSAGE=Hello from the mini-project!

Example configuration:
```bash
PORT=8090 SERVICE_NAME=my-midterm MESSAGE="Hello from my VM" npm start
```
For that port:
```bash
BASE_URL=http://localhost:8090 bash test/smoke.sh
```






## Part 2:) Docker Compose Inside a Linux VM
........................
- 



## Part 3:) Kubernetes Deployment
........................





## Part 4:) Controlled Troubleshooting
........................





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
