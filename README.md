Build and Push Docker Image
1. Build the Docker Image
docker build -t your-username/flask-app:latest .
2. Push the Docker Image to a Container Registry
Assuming you're using Docker Hub:
docker login
docker push your-username/flask-app:latest
Replace your-username with your Docker Hub username.

Kubernetes YAML Files
1. Setup Minikube
Install and start Minikube:
minikube start

3. Deploy MongoDB
kubectl apply -f mongodb-deployment.yml
kubectl apply -f mongodb-service.yml

5. Deploy Flask Application
kubectl apply -f flask-app-deployment.yml
kubectl apply -f flask-app-service.yml

7. Deploy Horizontal Pod Autoscaler
kubectl apply -f hpa.yml

5. Access the Flask Application
Expose the Flask application service via Minikube:
minikube service flask-app
This command will open a browser with the URL to access the Flask application.   

Explanation of DNS Resolution in Kubernetes
Within a Kubernetes cluster, DNS resolution for inter-pod communication is handled by CoreDNS (or kube-dns in older clusters). Each service in Kubernetes gets a DNS entry in the form of <service-name>.<namespace>.svc.cluster.local. For example:
Flask application: flask-app.default.svc.cluster.local
MongoDB service: mongodb.default.svc.cluster.local

Pods use these DNS names to communicate with each other. Kubernetes automatically updates the DNS records when services are created or updated.

Explanation of Resource Requests and Limits
Resource Requests: The amount of CPU and memory that Kubernetes guarantees to a container. It is used to decide on which node a pod should be scheduled.
Resource Limits: The maximum amount of CPU and memory a container can use. If a container exceeds these limits, it may be throttled or terminated.

Design Choices
Container Base Image: Used python:3.8-slim for a lightweight image.
HPA Configuration: Chose autoscaling/v2beta2 for more advanced scaling capabilities, but provided alternatives for backward compatibility.
Deployment Replicas: Set replicas: 2 to ensure high availability and fault tolerance.
Service Type: Used ClusterIP for internal communication between services within the cluster.

Cookie Point: Testing Scenarios
1. Simulate Load
Use tools like Apache Bench (ab) to generate load:
ab -n 1000 -c 10 http://<minikube-ip>/data

2. Monitor Autoscaling
Observe the HPA status before and after generating load:
kubectl get hpa

3. Verify Database Interaction
Post data to the /data endpoint and check retrieval:
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' http://<minikube-ip>/data
curl http://<minikube-ip>/data

4. Results and Issues
Autoscaling: Check if the number of replicas increased as expected under load.
Database Interaction: Ensure data is correctly inserted and retrieved from MongoDB.
