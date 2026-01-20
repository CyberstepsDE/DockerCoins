# Kubernetes Lab: DockerCoins Mining Rig

**Time:** 45-60 minutes

## Part 1: Start Your Kubernetes Cluster

```bash
minikube start
```

Verify the cluster is running:

```bash
kubectl get nodes
```

You should see `minikube` with status `Ready`.

## Part 2: Personalize Your Mining Rig

Open `webui/files/index.html` and find this line:

```html
<h1>DockerCoin Miner - YOUR_NAME</h1>
```

Replace `YOUR_NAME` with your actual name, for example:

```html
<h1>DockerCoin Miner - John Smith</h1>
```

Save the file.

## Part 3: Build and Push Docker Images

### Step 1: Log in to Docker Hub

```bash
docker login
```

### Step 2: Set your username

Replace `YOUR_DOCKERHUB_USERNAME` with your actual username:

```bash
export DOCKER_USER=YOUR_DOCKERHUB_USERNAME
```

Verify:

```bash
echo $DOCKER_USER
```

### Step 3: Build all images

```bash
docker build -t $DOCKER_USER/rng:v1 ./rng
docker build -t $DOCKER_USER/hasher:v1 ./hasher
docker build -t $DOCKER_USER/webui:v1 ./webui
docker build -t $DOCKER_USER/worker:v1 ./worker
```

### Step 4: Push to Docker Hub

```bash
docker push $DOCKER_USER/rng:v1
docker push $DOCKER_USER/hasher:v1
docker push $DOCKER_USER/webui:v1
docker push $DOCKER_USER/worker:v1
```

## Part 4: Deploy to Kubernetes

### Step 1: Create Deployments

```bash
kubectl create deployment redis --image=redis
kubectl create deployment rng --image=$DOCKER_USER/rng:v1
kubectl create deployment hasher --image=$DOCKER_USER/hasher:v1
kubectl create deployment worker --image=$DOCKER_USER/worker:v1
kubectl create deployment webui --image=$DOCKER_USER/webui:v1
```

### Step 2: Verify pods are running

```bash
kubectl get pods
```

Wait until all pods show `Running` status.

### Step 3: Create Services

```bash
kubectl expose deployment redis --port 6379
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
kubectl expose deployment webui --type=NodePort --port=80
```

### Step 4: Open Web UI

```bash
minikube service webui
```

Keep this terminal open! Open a new terminal for remaining commands.

You should see the DockerCoin Miner page with your name.

## Part 5: Scale Your Mining

In your new terminal, scale to 10 workers:

```bash
kubectl scale deployment worker --replicas=10
```

Watch the web UI - mining speed should increase!

## Part 6: Test Self-Healing

List worker pods:

```bash
kubectl get pods -l app=worker
```

Delete one pod (replace with actual pod name):

```bash
kubectl delete pod worker-XXXXX-XXXXX
```

Check pods again - Kubernetes automatically creates a replacement!

## Part 7: Rolling Updates and Rollback

Try updating to a non-existent version:

```bash
kubectl set image deployment/worker worker=$DOCKER_USER/worker:v2
```

Watch the rollout fail:

```bash
kubectl get pods
```

Rollback to working version:

```bash
kubectl rollout undo deployment/worker
```

## Submission

Take TWO screenshots:

1. Terminal with `kubectl get all` output
2. Web UI showing your name and mining graph

## Cleanup

```bash
minikube delete
```

See README.md for full cleanup instructions including Docker Hub.

