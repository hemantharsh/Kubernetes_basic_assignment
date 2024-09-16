# Kubernetes_basic_assignment
Here's a comprehensive README file for your GitHub repository, including all the important commands and troubleshooting tips:

---

# Simple Web Application Deployment with Docker and Kubernetes

This repository contains a simple Flask web application that has been Dockerized, deployed to a Kubernetes cluster, exposed via a Kubernetes Service, and scaled by increasing the number of replicas.

## Project Structure

- `app.py`: Python Flask application.
- `requirements.txt`: Python dependencies.
- `Dockerfile`: Dockerfile to build the Docker image.
- `deployment.yaml`: Kubernetes Deployment manifest.
- `service.yaml`: Kubernetes Service manifest.

## Steps to Reproduce

### 1. Create a Simple Flask Web Application

1. **Create Project Directory:**
   ```bash
   mkdir hello-world-app
   cd hello-world-app
   ```

2. **Create Flask Application (`app.py`):**
   ```bash
   nano app.py
   ```

   Add the following code:
   ```python
   from flask import Flask

   app = Flask(__name__)

   @app.route('/')
   def hello_world():
       return 'Hello, World!'

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=5000)
   ```

   Save and close the file.

3. **Create `requirements.txt`:**
   ```bash
   nano requirements.txt
   ```

   Add the following line:
   ```
   Flask==2.0.3
   ```

   Save and close the file.

### 2. Dockerize the Application

1. **Create a `Dockerfile`:**
   ```bash
   nano Dockerfile
   ```

   Add the following content:
   ```Dockerfile
   # Use an official Python runtime as a parent image
   FROM python:3.9-slim

   # Set the working directory in the container
   WORKDIR /app

   # Copy the current directory contents into the container at /app
   COPY . /app

   # Install any needed packages specified in requirements.txt
   RUN pip install --no-cache-dir -r requirements.txt

   # Make port 5000 available to the world outside this container
   EXPOSE 5000

   # Define environment variable
   ENV FLASK_APP=app.py

   # Run the application
   CMD ["python", "app.py"]
   ```

   Save and close the file.

2. **Build the Docker Image:**
   ```bash
   docker build -t hello-world-app .
   ```

3. **Test the Docker Image Locally (Optional):**
   ```bash
   docker run -p 5000:5000 hello-world-app
   ```

   Open your browser and navigate to `http://localhost:5000` to see the "Hello, World!" message.

### 3. Push Docker Image to a Container Registry

1. **Login to Docker Hub:**
   ```bash
   docker login
   ```

2. **Tag the Docker Image:**
   ```bash
   docker tag hello-world-app <your-dockerhub-username>/hello-world-app:latest
   ```

3. **Push the Image:**
   ```bash
   docker push <your-dockerhub-username>/hello-world-app:latest
   ```

### 4. Deploy the Application to a Kubernetes Cluster

1. **Create a Kubernetes Deployment Manifest (`deployment.yaml`):**
   ```bash
   nano deployment.yaml
   ```

   Add the following content:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hello-world-app
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: hello-world-app
     template:
       metadata:
         labels:
           app: hello-world-app
       spec:
         containers:
         - name: hello-world-app
           image: <your-dockerhub-username>/hello-world-app:latest
           ports:
           - containerPort: 5000
   ```

   Save and close the file.

2. **Deploy to Kubernetes:**
   ```bash
   kubectl apply -f deployment.yaml
   ```

3. **Verify Deployment:**
   ```bash
   kubectl get deployments
   kubectl get pods
   ```

### 5. Expose the Application Using a Kubernetes Service

1. **Create a Kubernetes Service Manifest (`service.yaml`):**
   ```bash
   nano service.yaml
   ```

   Add the following content:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: hello-world-service
   spec:
     selector:
       app: hello-world-app
     type: LoadBalancer
     ports:
       - protocol: TCP
         port: 80
         targetPort: 5000
   ```

   Save and close the file.

2. **Expose the Application:**
   ```bash
   kubectl apply -f service.yaml
   ```

3. **Get the External IP:**
   ```bash
   kubectl get service hello-world-service
   ```

   Once the external IP is available, open your browser and visit `http://<EXTERNAL-IP>` to see the "Hello, World!" message.

### 6. Scale the Application

1. **Update the Number of Replicas:**
   Edit the `deployment.yaml` file to increase the number of replicas:
   ```bash
   nano deployment.yaml
   ```

   Change `replicas` to 3:
   ```yaml
   replicas: 3
   ```

   Save and close the file.

2. **Apply the Updated Deployment:**
   ```bash
   kubectl apply -f deployment.yaml
   ```

3. **Verify Scaling:**
   ```bash
   kubectl get pods
   ```

   You should see 3 pods running now.
![image](https://github.com/user-attachments/assets/99a101ba-4aaf-4d3e-bb85-1bbf45ead780)


## Troubleshooting Commands

1. **Check Logs:**
   To view logs for a specific pod:
   ```bash
   kubectl logs <pod-name>
   ```

2. **Describe Resources:**
   To get detailed information about a deployment or service:
   ```bash
   kubectl describe deployment hello-world-app
   kubectl describe service hello-world-service
   ```

3. **Check for Issues:**
   To check for issues with pods:
   ```bash
   kubectl get pods
   kubectl describe pod <pod-name>
   ```

4. **Delete and Reapply Resources:**
   If you need to delete resources and reapply:
   ```bash
   kubectl delete -f deployment.yaml
   kubectl delete -f service.yaml
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

---

Feel free to modify any part of this README to better fit your specific use case or environment.
