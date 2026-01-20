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
## After delpoyment local webui should look like this:
`minikube service webui`
<img width="829" height="611" alt="image" src="https://github.com/user-attachments/assets/392ce2ff-228a-4fdc-852a-8623196c4ece" />


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
