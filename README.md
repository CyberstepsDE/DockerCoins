# DockerCoins - Kubernetes Learning Lab

A simple cryptocurrency mining simulator for learning Kubernetes concepts.

## What is DockerCoins?

DockerCoins is an educational application that simulates cryptocurrency mining. It consists of 5 microservices that work together:

- **rng** - Generates random bytes (Python/Flask)
- **hasher** - Computes SHA256 hashes (Ruby/Sinatra)
- **worker** - Coordinates the mining process (Python)
- **webui** - Shows mining statistics (Node.js/Express)
- **redis** - Stores data (standard Redis image)

## Architecture

```text
                     ┌──────────┐
                     │  webui   │ ◄── You view this in browser
                     └────┬─────┘
                          │ reads
                          ▼
┌────────┐  bytes   ┌──────────┐  hash   ┌──────────┐
│  rng   │ ◄─────── │  worker  │ ──────► │  hasher  │
└────────┘          └────┬─────┘         └──────────┘
                         │ writes
                         ▼
                    ┌──────────┐
                    │   redis  │
                    └──────────┘
```

See full walkthrough in [LAB.md]

## Before You Start

### Personalize Your WebUI

Edit `webui/files/index.html` and replace `YOUR_NAME` with your actual name:

```html
<h1>DockerCoin Miner - YOUR_NAME</h1>
```

Change to:

```html
<h1>DockerCoin Miner - John Smith</h1>
```

## Building and Pushing Images

Set your Docker Hub username:

```bash
export DOCKER_USER=your_dockerhub_username
```

Build and push all images:

```bash
# Build all images
docker build -t $DOCKER_USER/rng:v1 ./rng
docker build -t $DOCKER_USER/hasher:v1 ./hasher
docker build -t $DOCKER_USER/worker:v1 ./worker
docker build -t $DOCKER_USER/webui:v1 ./webui

# Push to Docker Hub
docker push $DOCKER_USER/rng:v1
docker push $DOCKER_USER/hasher:v1
docker push $DOCKER_USER/worker:v1
docker push $DOCKER_USER/webui:v1
```
## Create Deployments

```bash
kubectl create deployment redis --image=redis
kubectl create deployment rng --image=$DOCKER_USER/rng:v1
kubectl create deployment hasher --image=$DOCKER_USER/hasher:v1
kubectl create deployment worker --image=$DOCKER_USER/worker:v1
kubectl create deployment webui --image=$DOCKER_USER/webui:v1
```

Verify pods are running:

```bash
kubectl get pods
```

Wait until all pods show `Running` status.

## Create Services

```bash
kubectl expose deployment redis --port 6379
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
kubectl expose deployment webui --type=NodePort --port=80
```

## Open Web UI

```bash
minikube service webui
```

Your app will be automatically opened in a web browser:

<img width="1543" height="807" alt="image" src="https://github.com/user-attachments/assets/d96f5292-e82b-48d0-8816-5f6f6f179c6e" />


## Cleanup

When you want to start fresh or clean up after the lab:

### Step 1: Delete the Minikube cluster

```bash
minikube delete
```

This stops and removes the entire Kubernetes cluster.

### Step 2: Remove local Docker images

First, set your Docker Hub username (replace with your actual username):

```bash
export DOCKER_USER=your_dockerhub_username
```

Then remove the images:

```bash
docker rmi $DOCKER_USER/rng:v1
docker rmi $DOCKER_USER/hasher:v1
docker rmi $DOCKER_USER/worker:v1
docker rmi $DOCKER_USER/webui:v1
```

If you get "image is being used" errors, force removal with `-f` flag:

```bash
docker rmi -f $DOCKER_USER/rng:v1
docker rmi -f $DOCKER_USER/hasher:v1
docker rmi -f $DOCKER_USER/worker:v1
docker rmi -f $DOCKER_USER/webui:v1
```

### Step 3: Delete repositories from Docker Hub

1. Go to [Docker Hub](https://hub.docker.com)
2. Log in to your account
3. Click **Repositories**
4. For each repository (rng, hasher, worker, webui):
   - Click the repository name
   - Click **Settings** tab
   - Scroll down → **Delete repository**
   - Type the repository name to confirm

## License

This is a modernized version of the original DockerCoins demo by Jérôme Petazzoni.
Used for educational purposes.
