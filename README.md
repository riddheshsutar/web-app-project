### Step-by-Step Guide: Deploying a Web Server Using Docker and Kubernetes on Ubuntu

This guide will walk you through creating a simple web server application, containerizing it with Docker, pushing the image to Docker Hub, and deploying it on a Kubernetes cluster on Ubuntu.

---

#### **Prerequisites**

- **Ubuntu Machine**: Ensure you have an Ubuntu machine or server set up.
- **Docker Installed**: Install Docker on your Ubuntu machine.
- **Kubernetes Cluster**: Set up a Kubernetes cluster using Minikube or any other preferred method.
- **Docker Hub Account**: Create an account on Docker Hub to push your Docker images.

---

#### **Step 1: Create a Simple Web Server Application**

1. **Create a Project Directory**:
   ```bash
   mkdir web-app-project
   cd web-app-project
   ```

2. **Create a `Dockerfile`**:
   ```bash
   touch Dockerfile
   ```
   Add the following content to the `Dockerfile`:
   ```Dockerfile
   FROM nginx:latest
   COPY index.html /usr/share/nginx/html/index.html
   ```

3. **Create an `index.html` File**:
   ```bash
   touch index.html
   ```
   Add your desired HTML content:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Web Server</title>
   </head>
   <body>
       <h1>Welcome to the Web Server!</h1>
   </body>
   </html>
   ```

4. **Build the Docker Image**:
   ```bash
   docker build -t web-server-image .
   ```

---

#### **Step 2: Test the Docker Image**

1. **Run the Docker Container**:
   ```bash
   docker run -d -p 8085:80 web-server-image
   ```

2. **Access the Web Server**:
   - Open your web browser and navigate to `http://localhost:8085`.
   - You should see the content from your `index.html` file.

3. **Stop the Docker Container**:
   ```bash
   docker ps    # Get the container ID
   docker stop <container-id>
   ```

---

#### **Step 3: Push the Docker Image to Docker Hub**

1. **Tag the Docker Image**:
   ```bash
   docker tag web-server-image your-dockerhub-username/web-server-image:latest
   ```

2. **Log In to Docker Hub**:
   ```bash
   docker login
   ```

3. **Push the Image to Docker Hub**:
   ```bash
   docker push your-dockerhub-username/web-server-image:latest
   ```

4. **Verify the Image on Docker Hub**:
   - Log in to Docker Hub and ensure your image is listed under your repositories.

---

#### **Step 4: Set Up a Kubernetes Cluster**

1. **Install Minikube (Optional for Local Setup)**:
   Follow the [Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/) for Ubuntu.

2. **Start Minikube**:
   ```bash
   minikube start
   ```

---

#### **Step 5: Deploy the Web Server on Kubernetes**

1. **Create a Kubernetes Deployment Configuration (`web-server-deployment.yaml`)**:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: web-server-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: web-server
     template:
       metadata:
         labels:
           app: web-server
       spec:
         containers:
         - name: web-server
           image: your-dockerhub-username/web-server-image:latest
           ports:
           - containerPort: 80
   ```

2. **Apply the Deployment Configuration**:
   ```bash
   kubectl apply -f web-server-deployment.yaml
   ```

3. **Verify the Deployment**:
   ```bash
   kubectl get deployments
   ```

4. **Create a Kubernetes Service Configuration (`web-server-service.yaml`)**:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: web-server-service
   spec:
     selector:
       app: web-server
     ports:
     - protocol: TCP
       port: 80
       targetPort: 80
     type: NodePort
   ```

5. **Apply the Service Configuration**:
   ```bash
   kubectl apply -f web-server-service.yaml
   ```

6. **Verify the Service**:
   ```bash
   kubectl get services
   ```

---

#### **Step 6: Access the Web Server Application**

1. **Retrieve the Node Port**:
   ```bash
   kubectl get service web-server-service
   ```
   Note the `NodePort` assigned (e.g., `NodePort: 80 -> 80/TCP, 30008:80/TCP`).

2. **Access the Application**:
   - If using Minikube:
     ```bash
     minikube service web-server-service
     ```
     This command opens the service in your default web browser.
   - Alternatively, access the application via:
     ```
     http://<NodeIP>:<NodePort>
     ```
     Replace `<NodeIP>` with your cluster node IP and `<NodePort>` with the port retrieved earlier.

---

#### **Optional: Port Forwarding**

If you have trouble accessing the application, you can use port forwarding:

```bash
kubectl port-forward service/web-server-service 8085:80
```

Now, access the application at `http://localhost:8085`.

---

#### **Step 7: Clean Up Resources**

After testing, you can clean up the Kubernetes resources:

1. **Delete the Service and Deployment**:
   ```bash
   kubectl delete -f web-server-service.yaml
   kubectl delete -f web-server-deployment.yaml
   ```

2. **Stop Minikube (if used)**:
   ```bash
   minikube stop
   ```

---

#### **Conclusion**

You've successfully:

- Created a simple web server application.
- Containerized the application using Docker.
- Pushed the Docker image to Docker Hub.
- Deployed the application on a Kubernetes cluster.
- Accessed the application through a web browser.

This process ensures that your Docker image is accessible from Kubernetes without any errors and demonstrates a seamless workflow from development to deployment using Docker and Kubernetes on Ubuntu.

---

**Note**: Always replace `your-dockerhub-username` with your actual Docker Hub username throughout the commands.
