🚀 Docker to AWS EKS Deployment Guide

This guide explains step-by-step how to:

1.  Install required tools (AWS CLI, Docker, kubectl, eksctl)
2.  Build and run Docker image locally
3.  Push Docker image to DockerHub
4.  Create an EKS Cluster
5.  Deploy application to Kubernetes
6.  Expose application using LoadBalancer
7.  Access application over the web

------------------------------------------------------------------------

STEP 1: Install Required Tools

1. Install AWS CLI

sudo apt update 
sudo apt install awscli -y 
aws –version

Configure AWS CLI: aws configure Enter: - AWS Access Key - AWS Secret
Key - Region (e.g., ap-south-1) - Output format: json

------------------------------------------------------------------------

2. Install Docker

sudo apt update 
sudo apt install docker.io -y 
sudo systemctl start docker 
sudo systemctl enable docker 
sudo usermod -aG docker $USER

Verify: docker –version

------------------------------------------------------------------------

3. Install kubectl

curl -LO
“https://storage.googleapis.com/kubernetes-release/release/$(curl -s
https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl”
chmod +x kubectl 
sudo mv kubectl /usr/local/bin/

Verify: kubectl version –client

------------------------------------------------------------------------

4. Install eksctl

curl -sLO
“https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" 
tar -xzf eksctl_$(uname -s)_amd64.tar.gz sudo mv eksctl /usr/local/bin

Verify: eksctl version

------------------------------------------------------------------------

STEP 2: Build Docker Image

Inside your project directory (where Dockerfile exists):

docker build -t prime-video-app .

------------------------------------------------------------------------

STEP 3: Run Docker Container Locally

docker run -d -p 3000:3000 –name prime-container prime-video-app

Open in browser: http://localhost:3000

------------------------------------------------------------------------

STEP 4: Push Image to DockerHub

Login: docker login

Tag Image: docker tag prime-video-app yourdockerhubusername/prime-video-app:latest

Push Image: docker push yourdockerhubusername/prime-video-app:latest

------------------------------------------------------------------------

STEP 5: Create EKS Cluster

eksctl create cluster –name prime-cluster –region ap-south-1 –nodegroup-name prime-nodes –node-type t3.medium –nodes 2

Verify cluster: kubectl get nodes

------------------------------------------------------------------------

STEP 6: Create deployment.yaml

Create file: deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: prime-video-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: prime-video
  template:
    metadata:
      labels:
        app: prime-video
    spec:
      containers:
        - name: prime-video-container
          image: jainnikh3/prime_video:latest
          ports:
            - containerPort: 3000

Apply deployment: kubectl apply -f deployment.yaml 
kubectl get pods

------------------------------------------------------------------------

STEP 7: Create service.yaml (LoadBalancer)

Create file: service.yaml

apiVersion: v1
kind: Service
metadata:
  name: prime-video-service
spec:
  type: LoadBalancer
  selector:
    app: prime-video
  ports:
    - port: 80
      targetPort: 3000


Apply service: kubectl apply -f service.yaml 
kubectl get svc

------------------------------------------------------------------------

STEP 8: Access Application Over Web

Run: kubectl get svc

Copy the EXTERNAL-IP value.

Open in browser: http://

Your application is now live.

------------------------------------------------------------------------

Cleanup (Important to Avoid Charges)

eksctl delete cluster –name prime-cluster –region ap-south-1

------------------------------------------------------------------------

Complete Flow Summary:

Dockerfile → Docker Image → DockerHub → EKS Cluster → Deployment →
LoadBalancer → Public URL
