                                               **      Migration to Docker Kubernetes Project**

**Migration**

Migration is a  process of moving data, applications, or  from one environment to another Environment. This can involve transferring data between different databases, moving applications from on-premises servers to the cloud, upgrading systems, or shifting from one software platform to another.

**Migration to Docker Kubernetes Project**

Typically focuses on  transitioning applications  from traditional deployment methods to containerized environments using Docker and Kubernetes. 

**Transitioning applications** [process of moving applications from one environment to another]

**Traditional deployment** [process of releasing and managing software applications using conventional, manual, or semi-automated methods] 

**Containerized environments** [containers to deploy, manage, and run applications]




                                                    **Useful of this  Project**

                                  
**Learning Docker Basics:**
Understand how to create Docker images, manage containers, and write effective Dockerfiles.

**Kubernetes Concepts:**
Familiarize  with core concepts like pods, services, deployments, and persistent storage.

**Version Control:**
Use Git for version control of your project files, including Dockerfiles and Kubernetes manifests.  

**Dockerfile -** It automates the process of creating a Docker image by specifying the operating system,


**Configuration Management:**
Manage environment-specific configurations using ConfigMaps and Secrets in Kubernetes.

**Networking:** networking refers to how containers communicate with each other, the host system, and external networks. Proper networking is crucial for microservices and distributed systems to function effectively Understand Kubernetes networking concepts, including service discovery and ingress controllers.
                     
                     
                                                ** Key Concepts of Kubernetes Networking**

                                                
**1. Pod Networking**
**Pod-to-Pod Communication:** Every pod in a Kubernetes cluster can communicate with every other pod without using Network Address Translation (NAT). This is achieved through a flat network structure.
**IP Address:** Each pod gets a unique IP address, and all containers within a pod share the same network namespace, including the IP address and network ports.

**2. Service Networking**
**Services:** Kubernetes Services provide a stable IP address and DNS name to a set of pods, enabling reliable access to them even as pods are created and destroyed.
**ClusterIP:** Exposes the service on a cluster-internal IP, making it accessible only within the cluster.
**NodePort:** Exposes the service on a static port on each node’s IP, enabling external access to the service.
**LoadBalancer: ** Exposes the service externally using a cloud provider’s load balancer.
**ExternalName:** Maps the service to a DNS name, allowing access to external services.

**3. DNS Service Discovery**
**Internal DNS:** Kubernetes has an internal DNS service that automatically assigns DNS names to services, making it easy for pods to discover and communicate with services by name.
**Automatic Updates**: DNS entries are automatically updated as services are created or destroyed, ensuring continuous service discovery without manual intervention.

**4. Ingress Controllers**

**Ingress Resource:** Defines rules for how external HTTP/S traffic should be routed to services within the cluster. It provides load balancing, SSL termination, and name-based virtual hosting.
**Ingress Controller:** Implements the Ingress resource, managing the routing of HTTP/S traffic based on the defined rules. Popular ingress controllers include NGINX, Traefik, and HAProxy

                                                     
                                                       
                                                       ** Key Components of the Project**
                                                       
**Docker:**
**Containerization:** Docker allows you to package applications and their dependencies into containers, ensuring consistency across different environments.
**Dockerfile:** A script that contains instructions on how to build a Docker image for your application.

**Kubernetes:**
**Orchestration:** Kubernetes manages containerized applications across a cluster of machines, handling deployment, scaling, and networking.
**Resources:** Uses YAML files to define deployments, services, and other Kubernetes resources.

                                                     
                                                       
                                                       ** Benefits of the Project**
                                                       
**Scalability:** Easily scale applications up or down based on demand using Kubernetes' built-in features.

**Portability:** Containers can run on any machine that has Docker installed, making it easy to move between development, testing, and production environments.

**Resource Efficiency:** Containers are lightweight compared to traditional virtual machines, allowing for better utilization of system resources.

**Consistent Environments:** Development, testing, and production environments can be identical, reducing "it works on my machine" issues.

**Easier Management:** Kubernetes provides features for health checks, self-healing, and automated rollbacks, simplifying application management.




                                                                **Migration-to-Docker-Kubernetes-Project Steps Involved**


Step 1: mkdir migration Project

Step 2: cd Migration Project

Step 3: git clone https://github.com/BalagouniNaveen/migration-to-docker-kubernetes-Project/tree/main

Step 4: cd Docker-kubernetes-project

Step 5: **webDevelopment.yaml**
           
      **Kubectl apply -f webdevelopment.yaml -n Namespace**

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  namespace: migration-docker-kubernetes
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: my-web-app:latest
          ports:
            - containerPort: 5000
          env:
            - name: DATABASE_URL
              valueFrom:
                configMapKeyRef:
                  name: web-config
                  key: DATABASE_URL
      restartPolicy: Always

      

Step 6: **WebService.yaml**
 ** Kubectl apply -f webService.yaml -n Namespace**

 apiVersion: v1
kind: Service
metadata:
  name: web-service
  namespace: migration-docker-kubernetes
spec:
  selector:
    app: web
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
      

Step 7: **db-deployment.yaml**
  **Kubectl apply -f db-deployment.yaml -n Namespace**

apiVersion: apps/v1
kind: Deployment
metadata:
  name: db-deployment
  namespace: migration-docker-kubernetes
spec:
  replicas: 1
  selector:
    matchLabels:
      app: db
  template:
    metadata:
      labels:
        app: db
    spec:
      containers:
        - name: db
          image: postgres:13
          env:
            - name: POSTGRES_DB
              value: mydatabase
            - name: POSTGRES_USER
              value: user
            - name: POSTGRES_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: POSTGRES_PASSWORD
          volumeMounts:
            - name: db-storage
              mountPath: /var/lib/postgresql/data
      volumes:
        - name: db-storage
          persistentVolumeClaim:
            claimName: db-pvc
            

Step 8: **db-service.yaml**
**Kubectl apply -f db-service.yaml -n Namespace**

apiVersion: v1
kind: Service
metadata:
  name: db-service
  namespace: migration-docker-kubernetes
spec:
  selector:
    app: db
  ports:
    - protocol: TCP
      port: 5432
      

Step 9: **secret.yaml**
**Kubectl apply -f secret.yaml -n Namespace**

apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: migration-docker-kubernetes
type: Opaque
data:
  POSTGRES_PASSWORD: cGFzc3dvcmQ= # Base64 encoded password
  

Step 10: **configmap.yaml**
**Kubectl apply -f Configmap.yaml -n Namespace**

apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
  namespace: migration-docker-kubernetes
data:
  DATABASE_URL: postgres://user:password@db:5432/mydatabase



Step 11: **persistent-volume.yaml**
**Kubectl apply -f persistent-volume.yaml -n Namespace**

apiVersion: v1
kind: PersistentVolume
metadata:
  name: db-pv
  namespace: migration-docker-kubernetes
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  hostPath:
    path: /mnt/data


Step 12 **persistent-volume-claim.yaml:**
**Kubectl apply -f persistent-volume-claim.yaml -n Namespace**

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
  namespace: migration-docker-kubernetes
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
      

Step 12: **endpoint.yaml**  { Endpoints are used to specify the IP addresses and ports for the services that the Service resource should forward traffic to. Essentially, Endpoints define the network endpoints that a Service targets.}

**Kubectl apply -f endpoint.yaml -n Namespace**

apiVersion: v1
kind: Endpoints
metadata:
  name: web-service
  namespace: migration-docker-kubernetes
subsets:
  - addresses:
      - ip: 10.244.0.180
      - ip: 10.244.0.182
    ports:
      - port: 5000
        protocol: TCP
        

Step 13: **endpoint-patch.yaml**
**Kubectl apply -f endpoint-patch.yaml -n Namespace**

subsets:
- notReadyAddresses:
  - ip: 10.244.0.180
    nodeName: minikube
    targetRef:
      kind: Pod
      name: web-deployment-6c59c4f655-vcpl4
      namespace: migration-docker-kubernetes
      uid: 55d9181f-a9db-4f7e-aed4-407f98b9f35a
  - ip: 10.244.0.182
    nodeName: minikube
    targetRef:
      kind: Pod
      name: web-deployment-555d748c64-ck72l
      namespace: migration-docker-kubernetes
      uid: 395c0aa9-e123-4d4a-adfe-dbb73be28efb
  ports:
  - port: 5000
    protocol: TCP



