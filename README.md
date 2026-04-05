# Day 22: Kubernetes App Deployment in Minikube 🚀

This project demonstrates how to deploy a full-stack Node.js and MongoDB application to a local Kubernetes cluster using Minikube. It features a deployment, custom ConfigMaps, and NodePort services.

## 🏗️ Architecture

By splitting the containers into their own Deployments, we achieve true independent scalability. If the Node.js application requires more CPU, we can scale its replica count to 10 without needlessly duplicating the database.

### The Networking Shift (Internal DNS)
Because the Node.js app and MongoDB no longer share the same Pod, they can no longer communicate over `localhost` or `127.0.0.1`. 
- **The Solution:** A dedicated `ClusterIP` Service was created for the MongoDB database. 
- The Node.js application was updated to connect to the database using Kubernetes' built-in internal DNS.

## 📁 Infrastructure as Code (YAML Structure)

The infrastructure has been modularized into separate deployment files:

1. **Database Layer:**
   - `mongo-db.yaml`: Manages the MongoDB database Pods and A `ClusterIP` service providing a stable internal DNS name (`mongodb-service`) for the database.(combined dep and svc)
 
2. **Application Layer:**
   - `node-app.yaml`: Manages the Node.js web application Pods and A `NodePort` service to expose the frontend to the local browser.(combined dep and svc)

3. **Configuration:**
   - `mongo-config.yml`: Stores environment variables securely as a ConfigMap

## 🚀 Deployment Steps

### 1. Start the Cluster
Spin up your local Kubernetes environment using Docker as the driver:
```bash
minikube start --driver=docker

Build and push your image to Docker Hub:

docker build -t sagarpatade1900/node-mongo-db:v2 .
docker push sagarpatade1900/node-mongo-db:v2

3. Apply Kubernetes Manifests
Apply the configuration files to your cluster. This will provision the ConfigMap, the Deployment (with 3 replicas), and the Service.
# 1. Apply the db mongodb file
kubectl apply -f mongo-db.yml

# 2. Apply the ConfigMap (Environment Variables)
kubectl apply -f mongo-config.yml

# 2. Apply the Deployment and Service
kubectl apply -f node-app.yaml

4. Verify the Deployment
Check that all pods are running and the service is correctly provisioned:

kubectl get pods
kubectl get svc service-node-app

5. Access the Application
Since we are using Minikube with a NodePort service, use the built-in tunnel command to safely expose the application to your local browser:

minikube service service-my-nodedb-app