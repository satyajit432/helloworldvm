# Java Web Application: Hello World

This README provides a step-by-step guide to setting up a simple "Hello World" Java web application, containerizing it with Docker, and deploying it on Kubernetes.

---

## **Prerequisites**

Ensure you have the following installed:

- [Java JDK 8+](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html)
- [Maven](https://maven.apache.org/install.html)
- [Docker](https://docs.docker.com/get-docker/)
- [Kubernetes](https://kubernetes.io/docs/setup/) (Minikube or a Kubernetes cluster)
- [kubectl CLI](https://kubernetes.io/docs/tasks/tools/)

---

## 1. Creating the Java Web Application

### Step 1: Update the Web Application

1. Modify the `src/main/webapp/index.jsp` file to display "Hello World":

   ```jsp
   <html>
       <body>
           <h1>Hello World!</h1>
       </body>
   </html>
   ```

2. Add a `Dockerfile` in the root of your project:

   ```dockerfile
   FROM tomcat:9.0
   COPY target/hello-world.war /usr/local/tomcat/webapps/
   ```

3. Package the application as a WAR file:

   ```bash
   mvn package
   ```

---

## 2. Containerizing with Docker

1. Build the Docker image:

   ```bash
   docker build -t hello-world-app .
   ```

2. Run the Docker container:

   ```bash
   docker run -d -p 8080:8080 hello-world-app
   ```

3. Verify the application by visiting [http://localhost:8080/hello-world](http://localhost:8080/hello-world).

---

## 3. Deploying on Kubernetes

### Step 1: Create Kubernetes Deployment and Service

1. Create a deployment.yaml file:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: mydeploy
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: my-app
     template:
       metadata:
         labels:
           app: my-app
       spec:
         containers:
         - name: satyajitpani97/myimg
           image: myhelloworldimg
           ports:
           - containerPort: 8080
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: my-svc
   spec:
     selector:
       app: myy-app
     ports:
     - protocol: TCP
       port: 80
       targetPort: 8080
     type: NodePort
   ```

2. Apply the configuration:

   ```bash
   kubectl apply -f deployment.yaml
   ```

### Step 2: Access the Application

1. Get the NodePort of the service:

   ```bash
   kubectl get svc my-service
   ```

2. Access the application using the external IP and NodePort:

   ```
   http://<EXTERNAL-IP>:<NODE-PORT>/hello-world
   ```

---

## Cleanup

To remove the resources:

1. Stop and remove the Docker container:

   ```bash
   docker stop <CONTAINER_ID>
   docker rm <CONTAINER_ID>
   ```

2. Delete the Kubernetes deployment and service:

   ```bash
   kubectl delete -f -deployment.yaml
   ```

---

## Notes

- Ensure your Kubernetes cluster is running and configured correctly before deploying.
- Update `image` in the Kubernetes manifest file with the appropriate repository and tag if pushing to a container registry.

