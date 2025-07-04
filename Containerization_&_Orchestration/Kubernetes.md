#### Kubernetes (k8s) Architecture
- Kubernetes is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications across a cluster of nodes.
- It provides declarative configuration, service discovery, load balancing, self-healing, and storage orchestration.
- Kubernetes works with various container runtimes (e.g., Docker, containerd, CRI-O) and integrates with CI/CD pipelines for DevOps workflows.
- It follows a master-worker architecture, where the control plane (master nodes) manages scheduling and orchestration, while worker nodes execute the containerized workloads.
- Kubernetes operates on a master-worker node model, where the control plane (master) manages the cluster’s state, and worker nodes execute containerized workloads.

#### **1. Control Plane (Master Node):** The "brain" of the cluster, responsible for global decision-making and orchestration.
- API Server: The central hub for all cluster communications.
     - Exposes the Kubernetes API (used by kubectl, controllers, and internal components).
- Scheduler: Assigns pods to worker nodes based on resource availability, constraints, and policies.
- Controller Manager: Ensures the cluster’s desired state (e.g., replicas, node health) via control loops.
     - Includes sub-controllers (Deployment, Node, Endpoints, etc.).
- etcd: Distributed key-value store that persists cluster state (configurations, secrets, metadata).

#### **2. Worker Nodes (Data Plane):** Machines that run containerized applications. Key components:
- Kubelet: Node agent that communicates with the control plane.
     - Ensures containers are running in pods as specified (e.g., health checks).
- Kube-proxy: Manages network rules (IP tables/IPVS) to enable service discovery and load balancing.
     - Routes traffic to pods based on Service definitions.
- Container Runtime: Software (e.g., Docker, containerd, CRI-O) that pulls images and runs containers.
- Interfaces with Kubernetes via the Container Runtime Interface (CRI).
- The master node manages and monitors everything, while the worker nodes actually run the workloads.
- This architecture allows Kubernetes to provide automated scaling, self-healing, and high availability for your applications.
     - **Pods** are the smallest deployable units in Kubernetes, similar to lightweight VMs. They can contain one or more containers & are ephemeral, receiving new IPs each time they restart.
     - **kubectl** is a command-line tool for interacting with the Kubernetes cluster, supporting both imperative and declarative management.

#### Example for Beginners
- Think of Kubernetes like a warehouse manager (control plane) directing workers (nodes) to efficiently pack, ship, and monitor containers (your apps).

#### Visual Analogy
- Imagine Kubernetes as a shipping company:
     - Control Plane = Logistics HQ (tracks shipments, assigns trucks).
     - Worker Nodes = Delivery trucks (execute shipments/containers).
     - etcd = Warehouse ledger (records all transactions).

#### Kubernetes Objects Overview
- **1. Workload Objects:** Purpose: Define and manage application workloads.
     - Pod: Smallest deployable unit; contains one or more containers sharing resources (network, storage).
     - ReplicaSet: Ensures a stable number of identical pod replicas are running (used indirectly by Deployments).
     - Deployment: Manages ReplicaSets with declarative updates, rollbacks, and scaling (e.g., kubectl rollout undo).
     - StatefulSet: For stateful apps (e.g., databases). Provides:
     - Stable, ordered pod names (pod-0, pod-1).
     - Persistent storage (via PVCs).
     - DaemonSet: Runs one pod per node (e.g., log collectors like fluentd, node monitoring).
     - Job: Runs a batch task to completion (e.g., data processing).
     - CronJob:Schedules Jobs (e.g., nightly backups).
- **2. Service & Networking Objects:** Purpose: Enable connectivity and traffic management.
     - Service: Exposes pods as a network service:
     - ClusterIP (internal), NodePort (external access), LoadBalancer (cloud LB), ExternalName (DNS alias).
     - Ingress: Manages external HTTP/S traffic (path-based routing, TLS termination). Requires an Ingress Controller (e.g., Nginx, Traefik).
     - Egress: Policies for outbound traffic (e.g., restricting access to external APIs via NetworkPolicy).
     - Endpoint: Dynamic list of pod IPs for a Service (auto-updated).
     - NetworkPolicy: Firewall rules for pods (e.g., "Allow frontend pods to talk to backend pods on port 5432").
- **3. Configuration & Storage Objects:** Purpose: Manage app configs and persistent data.
     - ConfigMap: Stores non-sensitive configs (e.g., environment variables, config files).
     - Secret: Stores sensitive data (e.g., passwords, TLS certs) as base64-encoded strings.
     - PersistentVolume (PV): Cluster-wide storage resource (e.g., AWS EBS, NFS).
     - PersistentVolumeClaim (PVC): "Request" for storage by a pod (binds to a PV).
     - StorageClass: Defines dynamic provisioning (e.g., "fast-ssd" vs. "standard-hdd").
- **4. Cluster Management Objects:** Purpose: Administer cluster security and resources.
     - Namespace: Virtual cluster isolation (e.g., dev, prod).
     - Node: Worker machine (physical/virtual) running pods.
     - Role & RoleBinding: 
          - Role: Permissions within a namespace (e.g., "read pods").
          - RoleBinding: Assigns a Role to users/groups.
     - ClusterRole & ClusterRoleBinding: Applies permissions cluster-wide (e.g., "list all nodes").
     - ServiceAccount: Identity for pods to authenticate with the API server.

#### Scheduler 
- Scheduler assigns new pods to appropriate worker nodes based on available resources, affinity rules, and constraints.
     - It considers CPU, memory, affinity, taints, and tolerations when selecting a node.
- Use Case:
     - When a new deployment happens, the scheduler automatically places pods on the best-fit node.
     - In a multi-node cluster, it ensures efficient utilization of resources.

#### Controller Manager
- its a component that runs controller loops to maintain the desired state of the cluster.
     - It includes controllers like Node, Replication, ServiceAccount, and Endpoints Controllers.
- Use Case:
     - Ensures high availability by replacing failed pods.
     - Maintains desired replica count for Deployments, ReplicaSets, etc.

#### Etcd
- its a key-value store that maintains the entire cluster state.
     - Used for storing pod, service, and node configurations.
- Use Case:
     - Ensures cluster consistency and state recovery in case of node failures.
     - Used in multi-master setups for high availability.

#### Kube-proxy
- its a networking component that handles request forwarding and maintains network rules.
     - Uses iptables, IPVS, or eBPF for routing traffic.
- Use Case:
     - Ensures traffic from a Service reaches the correct Pod.
     - Implements load balancing for Services inside the cluster.

#### Container Runtime
- it is responsible for running containers.
     - Supported runtimes include Docker, containerd, and CRI-O.
- Use Case:
     - Runs application workloads inside Kubernetes.
     - Supports container networking and storage.

#### ReplicaSet
- its a specified number of identical pods are running.
     - Automatically scales up/down based on desired replica count.
- Use Case:
     - Ensures fault tolerance by replacing failed pods.
     - Used in Deployments to achieve rolling update

#### DaemonSet
- it see's pod runs on every node in the cluster.
     - Used for running monitoring and logging agents.
- Use Case:
     - Deploying Prometheus node exporter on every node.
     - Running Fluentd or Logstash to collect logs from all nodes.

#### Job in kubernetes
- it Runs a one-time task and see's it completes successfully.
- Use Case:
     - Data migration before deploying a new application version.
     - Database backup jobs that should run only once.

#### CronJob
- it is a  scheduled Job that runs at a specific time, similar to a Linux cron job.
- Use Case:
     - Running a daily database backup at midnight.
     - Sending automated reports every week.

#### Service
- it Exposes a group of pods to the network.
     - Types: ClusterIP, NodePort, LoadBalancer, ExternalName.
- Use Case:
     - ClusterIP: Internal communication between services.
     - NodePort: Exposing services to the external network.
     - LoadBalancer: Integrating with cloud-based load balancers.

#### Egress
- Controls outbound network traffic from the cluster.
     - Ensures security policies for external communication.
- Use Case:
     - Restricting external API calls from certain namespaces.
     - Allowing access only to trusted external services.

#### Endpoint
- Represents the IP addresses of pods associated with a Service.
- Use Case:
     - When a pod is added or removed, the endpoint list is updated automatically.

#### NetworkPolicy
- Defines rules to control traffic between pods.
- Use Case:
     - Allow only frontend pods to communicate with backend pods.
     - Block traffic from unauthorized namespaces.

#### ConfigMap
- Stores configuration data in key-value format.
- Use Case:
     - Store database connection strings or environment variables.
     - Used for externalizing configurations instead of hardcoding values.

#### Kubernetes Secret
- Stores sensitive data (passwords, API keys) securely.
- Use Case:
     - Storing Docker registry credentials for private images.
     - Managing TLS certificates for secure communication.

#### StorageClass
- Defines dynamic storage provisioning in Kubernetes.
- Use Case:
     - Provisioning persistent volumes automatically.
     - Using different storage backends like AWS EBS, Azure Disk, NFS.

#### Kubernetes Namespace
- Provides logical isolation for different teams or environments.
- Use Case:
     - Separate namespaces for dev, staging, and production.
     - Multi-tenant clusters where different teams use different namespaces.

#### Role & RoleBinding
- Role: Defines permissions within a namespace.
- RoleBinding: Grants access to a specific user or service account.
- Use Case:
     - Giving read-only access to developers for a namespace.
     - Allowing CI/CD pipeline to modify only specific resources.

#### ClusterRole & ClusterRoleBinding
- ClusterRole: Provides permissions across the entire cluster.
- ClusterRoleBinding: Assigns a ClusterRole to a user or service.
- Use Case:
    - Admin users need cluster-wide access to all namespaces.
    - Granting monitoring tools (like Prometheus) access to all namespaces.

#### ServiceAccount
- Provides identity to pods for interacting with Kubernetes API.
- Use Case:
     - Granting permissions to a CI/CD pipeline running inside Kubernetes.
     - Authenticating pods without using user credentials.

#### Node Pool
- A group of worker nodes in Kubernetes with the same configuration. Used for workload separation (e.g., general workloads vs GPU workloads).
- Use Case: Running GPU workloads separately from general workloads.
     - Example : az aks nodepool add

#### Resources in k8s
- Defines CPU & memory limits for containers. Helps in optimizing performance & cost.
- Use Case: Prevents over-utilization of cluster resources.
     - Example : requests, limits

#### Requests
- Minimum resources required for a container. Helps scheduler in placing pods on suitable nodes.
- Use Case: Ensures pods run without resource starvation.
     - Example : requests.cpu, requests.memory 

#### MatchLabels & Selector
- Used for selecting pods based on labels. Used in Services, Deployments, ReplicaSets, etc.
- Use Case: Selects only pods labeled with app=nginx for deployment.
     - Example : matchLabels: app: nginx

#### Metadata
- Stores name, labels, and annotations of Kubernetes objects.
- Use Case: Helps categorizing and filtering resources efficiently.
     - Example : labels, annotations
       
#### Spec (Specification)
- Defines configuration details for objects like Pods, Deployments, Services, etc.
- Use Case: Defines how Kubernetes objects behave.
     - Example : spec: replicas: 3

#### Sidecar Container & Init Container
- Sidecar Containers: Extend the functionality of the main container.
     - Logs application traffic.
     - Example : fluentd logging sidecar
- Init Containers: Run before the main container starts.
     - Ensures database migration is completed before app starts.
     - Example : db-migration init container

#### Imperative vs Declarative
- Direct commands to create/update resources
     - kubectl create deployment nginx --image=nginx
     - Example : kubectl create deployment
- Uses YAML files to define desired state
     - kubectl apply -f deployment.yaml
     - Example : kubectl apply -f

#### Node Selector
- Schedules pods on specific nodes.
     - Example : nodeSelector: disktype: ssd
- Use Case: Ensures high-performance workloads run on SSD-backed nodes.

#### Container Network Interface (CNI) Plugin
- Manages network connectivity between pods in Kubernetes. Popular CNIs include Calico, Flannel, Cilium.
     - Example : Calico, Flannel, Cilium
- Use Case: Enables pod-to-pod networking and network policies.

#### Cloud-Lint
- A cloud configuration scanning tool that detects misconfigurations in Terraform, AWS, Azure, GCP.
     - Example : tflint --init
- Use Case: Prevents security misconfigurations in IaC deployments.

#### Elastic HA (High Availability)
- Ensures redundancy and fault tolerance in cloud environments.
     - Example : RollingUpdate strategy 
- Use Case: Ensures zero downtime during deployments.

#### Content Delivery Network (CDN)
- Distributes cached static content closer to users for faster loading speeds.
     - Example : AWS CloudFront
- Use Case: Speeds up content delivery for globally distributed applications

#### Front-end Pod
- A front-end pod hosts UI-related services like web applications. Typically exposed via a Service or Ingress Controller.
- Use Case:
     - Hosting React/Angular-based web applications.
     - Exposing UI via LoadBalancer or Ingress.
- Example : An E-commerce website where the frontend pod serves static HTML/JS files while the backend pod handles business logic.

#### Back-end Pod
- A back-end pod handles business logic, APIs, and database interactions.
- Use Case:
     - Running APIs (REST, GraphQL, gRPC).
     - Processing user requests before sending data to frontend.
- Example : A Node.js backend API that interacts with MongoDB or PostgreSQL.

#### ETL Pipeline
- ETL - (Extract, Transform, Load) - An ETL pipeline extracts data from a source, processes it, and loads it into a data warehouse.
- Use Case:
     - Processing large amounts of data for analytics.
     - Automating data transformation workflows.
- Example : Data pipelines for ML models, where raw data is processed, transformed, and stored in a data warehouse.
     - Apache Airflow CronJob

#### Labels
- Labels are key-value pairs used to identify and group Kubernetes objects.
- Use Case:
     - Grouping pods for deployments and services.
     - Filtering logs in monitoring tools (Prometheus, Grafana).
- Example : CI/CD pipelines use labels to deploy only specific environments.
     - labels: env: production

#### Key-Value Pairs
- Used in ConfigMaps, Secrets, and Labels.
- Use Case: Storing environment variables for applications.
- Example : Using ConfigMaps to manage database connection strings.
     - ConfigMaps and Secrets

#### Kind
- The kind field in Kubernetes YAML defines the type of resource being created.
- Use Case: Distinguishing different Kubernetes resources (e.g., Pod, Service, Deployment).
- Example : Managing multiple Kubernetes resources efficiently in YAML files.
     - kind: Deployment, kind: Service

#### Socket Layer
- The socket layer is a critical component in networking that provides an interface for communication between applications over a network. In Kubernetes, sockets enable inter-pod and external communication.
- How It Works in Kubernetes
     - Used for service-to-service communication inside a Kubernetes cluster.
     - Relies on container networking (CNI plugins) to establish connections.
     - Used in Ingress, LoadBalancers, and Services for external communication.
- Use Case:
     - Microservices architecture where one pod communicates with another over a socket connection.
     - Database connections between an application pod and a database service.
- Example : A Node.js API server communicating with a PostgreSQL database using a socket connection.
     - Service with ClusterIP or LoadBalancer

#### ReplicationController
- The ReplicationController (RC) ensures that a specified number of pod replicas are running at all times. However, it's deprecated in favor of Deployments and ReplicaSets.
- Key Features
     - Ensures desired state of pod replicas.
     - Recreates failed pods automatically.
     - Scales applications horizontally.
- Use Case:
     - Running multiple instances of a stateless application to improve reliability and availability.
- Example : A frontend service with 3 replicas to handle traffic.
     - kind: ReplicationController (Deprecated)

#### APIVersion
- The apiVersion field in Kubernetes specifies the Kubernetes API group and version that a resource belongs to
- Use Case:
     - Ensuring backward compatibility when upgrading Kubernetes clusters.
     - Using the correct API group for managing Kubernetes objects.
- Example : Migrating from extensions /v1 beta1 to apps/v1 for Deployments.
     - apiVersion: apps/v1

#### Pod.yaml
- A Pod YAML file defines the configuration of a pod, including its containers, resources, and networking details.
- Configuration Files:
     - pod.yaml (Defines a pod)
     - service.yaml (Exposes the pod)
     - deployment.yaml (Manages pod scaling)
- Use Case: Deploying applications inside Kubernetes.
- Example : Running a Python Flask application inside a pod.
     - apiVersion: v1, kind: Pod

#### Kubectl commands
1. Basic Commands
     - kubectl version  ( Show kubectl and API server versions )
     - kubectl cluster-info  	(Show cluster information )
     - kubectl config view  	(View kubeconfig settings)
     - kubectl get nodes	List all nodes in the cluster
     - kubectl describe node <node-name>	Show detailed info about a node
2. Context and Configuration
     - kubectl config get-contexts	List available contexts
     - kubectl config current-context	Show the current context
     - kubectl config use-context <context>	Switch to a different context
     - kubectl config set-context <context>	Set a new context
3. Workloads: Pods, Deployments, and ReplicaSets
     - kubectl get pods	List all pods
     - kubectl get pods -o wide	List pods with extra details
     - kubectl describe pod <pod-name>	Show detailed pod information
     - kubectl logs <pod-name>	View logs of a pod
     - kubectl logs -f <pod-name>	Follow live logs
     - kubectl exec -it <pod-name> -- /bin/sh	Open a shell inside a pod
     - kubectl delete pod <pod-name>	Delete a specific pod
     - kubectl delete pods --all	Delete all pods
     - kubectl rollout status deployment <deployment-name>	Check deployment rollout status
     - kubectl rollout restart deployment <deployment-name>	Restart a deployment
4. Creating and Managing Resources
     - kubectl apply -f <file>.yaml	Apply configuration from a file
     - kubectl create deployment <name> --image=<image>	Create a deployment
     - kubectl delete -f <file>.yaml	Delete resources from a file
     - kubectl scale deployment <deployment-name> --replicas=<number>	Scale a deployment
     - kubectl edit deployment <deployment-name>	Edit a deployment resource
5. Services and Networking
     - kubectl get svc	List all services
     - kubectl describe svc <service-name>	Show detailed service information
     - kubectl expose deployment <deployment-name> --type=NodePort --port=<port>	Expose a deployment as a service
     - kubectl port-forward <pod-name> <local-port>:<pod-port>	Forward a local port to a pod
6. ConfigMaps and Secrets
     - kubectl get configmaps	List all ConfigMaps
     - kubectl describe configmap <configmap-name>	Show ConfigMap details
     - kubectl create configmap <name> --from-literal=key=value	Create a ConfigMap
     - kubectl get secrets	List all Secrets
     - kubectl describe secret <secret-name>	Show secret details
7. Namespaces
     - kubectl get namespaces	List all namespaces
     - kubectl create namespace <namespace>	Create a new namespace
     - kubectl delete namespace <namespace>	Delete a namespace
8. Persistent Volumes and Storage
     - kubectl get pv	List Persistent Volumes (PV)
     - kubectl get pvc	List Persistent Volume Claims (PVC)
9. Debugging and Troubleshooting
     - kubectl describe <resource> <name>	Detailed resource information
     - kubectl logs <pod-name>	View logs
     - kubectl exec -it <pod-name> -- /bin/sh	Open a shell inside a pod
     - kubectl get events	Show cluster events
     - kubectl top pod	Show CPU and memory usage for pods
     - kubectl top node	Show CPU and memory usage for nodes
10. Advanced Commands
     - kubectl apply -k <directory>	Apply a Kustomization
     - kubectl diff -f <file>.yaml	Show differences before applying changes
     - kubectl explain <resource>	Show resource documentation
     - kubectl label pod <pod-name> key=value	Add a label to a pod
     - kubectl annotate pod <pod-name> key=value	Add an annotation to a pod

#### Kubelet
- Kubelet is a critical node-level agent in Kubernetes that ensures containers are running in a Pod. It communicates with the Kubernetes API server, manages container lifecycles, and monitors health.
     - Registers the node with the Kubernetes API server.
     - Starts and stops containers inside Pods.
     - Manages Pod lifecycle and ensures containers run as expected.
     - Monitors Pod health using liveness & readiness probes.
     - Communicates with container runtime (Docker, containerd, CRI-O) to manage containers.

- **Role of Kubelet in Kubernetes**
     - The kubelet is a node agent that manages pod lifecycle, health checks, and container runtime interactions.
     - It ensures that the desired state of Pods matches the actual state

- How does Kubelet interact with the container runtime
     - Kubelet uses the Container Runtime Interface (CRI) to interact with container runtimes like Docker, containerd, CRI-O.
     - It sends commands to start, stop, and monitor containers.

- Kubelet process stops
     - The node will stop reporting to the Kubernetes API server.
     - Pods running on that node may become unreachable.
     - controller manager will schedule new pods on available nodes if configured properly.

#### Pods
- A Pod is the smallest deployable unit in Kubernetes that runs one or more containers with shared networking and storage.
- **Pod Architecture & Components**
     - Containers: Main application + sidecar containers.
     - Network: Every Pod gets a unique IP and can communicate with other Pods.
     - Storage Volumes: Shared storage between containers in a Pod.
- What happens when a Pod fails
     - If managed by a Deployment/ReplicaSet, Kubernetes will reschedule it.
     - If a standalone Pod, it must be recreated manually.
- Pod have multiple containers
     - multiple containers in a Pod can share resources and work together (e.g., an app container + a logging sidecar).
- How do Pods communicate with each other
     - Each Pod gets a unique IP address.
     - Pods communicate within the cluster via Kubernetes networking (CNI plugins like Calico, Flannel, Cilium).
- info
     - Simplifi ed application management
     - Improved scaling and availability
     - Easy deployment and rollback
     - Improved resource utilizatio
     - Increased portability and fl exibility
     - A pod is the smallest deployable unit in Kubernetes that represents a single instance of a running process in a container.

- Conclusion
     - Pods are the smallest deployable unit in Kubernetes.
     - Single-container Pods run simple applications, while multi-container Pods use Sidecar patterns.
     - Pods are managed by controllers like Deployments, StatefulSets, and DaemonSets.
     - Networking, storage, and scheduling are important considerations when deploying Pods.

#### Pod Lifecycle
- Pods go through different phases during their lifecycle:
     - **Phase** -	Description
     - **Pending** - 	The Pod is created but waiting for resources.
     - **Running** -	At least one container is running.
     - **Succeeded** -	All containers have successfully completed execution.
     - **Failed** - 	One or more containers exited with an error.
     - **Unknown** -	The Pod state cannot be determined.

#### Pod Restart Policies
- A Pod's restart policy controls how Kubernetes handles failed containers:
     - **Always** -	Restarts the container if it fails (default).
     - **OnFailure** -	Restarts only if it fails with an error.
     - **Never** -	Does not restart the container.

#### How Pods Are Managed
- Pods are usually managed by Controllers:
     - **Deployment** → Scales and manages ReplicaSets.
     - **StatefulSet** → Manages stateful applications.
     - **DaemonSet** → Runs a Pod on every node.
     - **Job & CronJob** → Runs Pods for batch processing.

#### Pod Networking
- details
     - Each Pod gets a unique IP.
     - Containers within a Pod share the same network namespace.
     - Communication between Pods happens using ClusterIP Services.

**pod pending**
- The node has a taint, which prevents scheduling unless the pod has a matching toleration.
     - we can add a toleration in the pod spec (if pod should run on tainted node)
     - we can remove the taint from the node (if not needed)
     - we can Use nodeSelector or nodeAffinity instead of taints (if controlling placement)
- **taint** is a rule applied to a Kubernetes node that prevents certain pods from being scheduled on it unless the pod has a matching toleration

#### Stateful & Stateless
- **stateful application** maintains data or session state, like databases or message queues. Kubernetes uses resources like StatefulSets and PersistentVolumes to manage stateful applications, ensuring stable network identities and storage persistence across pod restarts or rescheduling
     - Stateful apps retain data and require persistent storage. They use StatefulSets, each pod has a stable identity and its own storage. Example: databases like MySQL.

- **stateless application** does not store any data or client session information between requests. It can be easily scaled horizontally because all replicas behave the same — examples include web servers or front-end services
     - Stateless apps don’t store data between requests. They use Deployments, are easy to scale, and all pods are identical. Example: web servers.

#### StatefulSet
- StatefulSet is a Kubernetes controller used to manage stateful applications that require:
     - Stable network identities (e.g., pod-0, pod-1, pod-2).
     - Persistent storage that remains even if the Pod is restarted.
     - Ordered and controlled scaling (both up and down).
     - Common Use Cases:
          - Databases (MySQL, PostgreSQL, MongoDB).
          - Message Queues (Kafka, RabbitMQ).
          - Distributed Applications (Elasticsearch, Zookeeper)

#### ClusterIP
- it is internal communication between pods within the cluster. It assigns an internal IP and is only accessible inside the cluster.
- It's used for backend services, databases, and internal APIs. For external access, LoadBalancer, NodePort, or Ingress is used.

#### ExternalName
- it maps a service name to an external DNS instead of routing traffic to internal pods.
- It’s used to access external services like third-party APIs or databases using Kubernetes DNS, without exposing cluster resources.

#### Deployment
- A Deployment in Kubernetes manages the rollout and scaling of Pods. It ensures:
     - Declarative updates to applications.
     - Automated rollbacks if failures occur.
     - Scaling of replicas based on demand.
     - Rolling updates & zero downtime deployments.
     - Use Cases:
          - Deploying stateless applications (e.g., Nginx, API services).
          - Managing multiple replicas for high availability.
          - Performing rolling updates & rollbacks safely.
- Deployment Works
     - Define a Deployment (specifies the desired state).
     - Kubernetes creates and manages ReplicaSets, ensuring the correct number of Pods run.
     - Handles updates & rollbacks automatically to prevent downtime.
     - Uses labels and selectors to group Pods efficiently.

#### Stateful
- they are applications store and maintain data between requests. Unlike stateless applications, they require persistent storage and cannot be easily replaced without losing data.
- Key Characteristics of Stateful Applications:
     - Data is stored and maintained across sessions.
     - Requires persistent storage (databases, logs, etc.).
     - Scaling is complex due to data consistency needs.
     - Failure of a node may require data recovery or replication.
- Examples of Stateful Applications:
     - Databases: MySQL, PostgreSQL, MongoDB.
     - Message Queues: Kafka, RabbitMQ.
     - Storage Systems: ElasticSearch, Cassandra.
     - Applications with user sessions: Banking apps, e-commerce carts.

#### Stateless
- Stateless applications do not store data or session state on the server. Each request is independent and does not rely on previous requests.
- Key Characteristics:
     - No persistent storage needed.
     - Each request is handled separately.
     - Can be easily scaled horizontally (add more replicas).
     - Failure of a single instance does not affect the overall system.
- Examples of Stateless Applications:
     - Web servers (Nginx, Apache).
     - RESTful APIs.
     - Microservices.
     - Containers running ephemeral workloads.

#### Microservices
- its an architectural style where an application is built as a collection of small, independent services that communicate over a network (usually via APIs).
- Each microservice is responsible for a specific business functionality and can be developed, deployed, and scaled independently
     - **Large-Scale Applications**: If your application is complex and needs independent scaling.
     - **Frequent Deployments**: When you need CI/CD pipelines and faster releases.
     - **Multiple Development Teams**: Ideal when teams work on separate services.
     - **Cloud & Container-Based Deployments**: Best suited for Kubernetes, Docker, AWS Lambda.
     - **Business Domain Separation**: When different parts of your system have distinct business logic.

##### Nodeport
- it exposes a service on a static port on all nodes in the cluster. This allows external traffic to access the service using NodeIP:NodePort.
- NodePort Works
     - A NodePort service selects a pod using labels.
     - Kubernetes assigns a static port (default range: 30000–32767).
     - The service becomes accessible via http://<NodeIP>:<NodePort>.
     - Any request to this port is forwarded to the underlying pods

#### Node
- its the fundamental compute unit in a Kubernetes cluster where containers run. Nodes are worker machines (can be physical or virtual) that execute Pods.
- Each node contains essential services to run containers, such as:
     - Kubelet – Communicates with the control plane and manages pods.
     - Container Runtime – Runs containerized applications (Docker, containerd, CRI-O).
     - Kube Proxy – Handles networking and service communication.

#### Service Mesh
- its an infrastructure layer that manages service-to-service communication in microservices, enhancing security, observability, reliability without changing application code.
     - It abstracts networking complexities and ensures seamless service interactions.
     - its a dedicated networking layer that helps manage communication between microservices in a Kubernetes cluster.
     - It provides traffic control, security, observability, and reliability without modifying the application code
- 𝗔𝗿𝗰𝗵𝗶𝘁𝗲𝗰𝘁𝘂𝗿𝗲
     - **Data Plane** – Handles the actual traffic flow between services, typically using sidecar proxies like Envoy.
     - **Control Plane** – Manages policies, security, service discovery, and observability for better control and governance.
- 𝗞𝗲𝘆 𝗕𝗲𝗻𝗲𝗳𝗶𝘁𝘀
     - Traffic Control – Load balancing, failover, retries
     - Security – Mutual TLS (mTLS), authentication, authorization, zero-trust security
     - Observability – Distributed tracing, logging, metrics for debugging and monitoring
     - Resilience – Circuit breaking, rate limiting, retries, fault injection
     - Multi-cluster & Multi-cloud Support – Connects services across Kubernetes clusters and hybrid cloud environments

- 𝗪𝗵𝗲𝗻 𝘁𝗼 𝗨𝘀𝗲 𝗮 𝗦𝗲𝗿𝘃𝗶𝗰𝗲 𝗠𝗲𝘀𝗵?
     - Large-scale microservices architectures with complex service-to-service communication
     - Need for end-to-end security with zero-trust networking (mTLS, authorization)
     - Deep observability for debugging, monitoring, and tracing service interactions
     - Managing multi-cluster or hybrid cloud deployments
     - Requirement for advanced traffic policies like canary deployments, blue-green deployments

- 𝗣𝗼𝗽𝘂𝗹𝗮𝗿 𝗦𝗲𝗿𝘃𝗶𝗰𝗲 𝗠𝗲𝘀𝗵𝗲𝘀
     - Istio – Feature-rich, Kubernetes-native, integrates well with K8s RBAC and policies
     - Linkerd – Lightweight, easy to use, lower resource footprint
     - HashiCorp Consul – Multi-platform, supports VM and Kubernetes environments, strong service discovery capabilities
     - Kuma – Built on Envoy, supports both Kubernetes and VMs, good for multi-cloud setups
     - AWS App Mesh – Fully managed, deeply integrated with AWS services

#### Stateless vs. Stateful

|Feature|Stateless|Stateful|
|- |-|-|
|Data Storage|No persistent storage|Uses persistent storage|
|Session Handling|Each request is independent|Requires session tracking|
|Scaling|Easily scalable|More complex to scale|
|Resilience|Any instance can handle requests|Requires coordination between instances|
|Use Case|Web servers, APIs, microservices|Databases, Kafka, Zookeeper|

#### PV - Persistent Volumes
- those are storage resource that provides persistent storage to applications, ensuring that data remains available even if a Pod restarts or is rescheduled to another node.
- those are storage resources that exist independently of Pods. They provide a way to retain data even if a Pod is deleted, rescheduled, or crashes.
     - **Decouples storage from Pods** – Data persists beyond Pod lifecycles.
     - **Supports dynamic provisioning** – Automatically creates storage on demand.
     - **Multiple storage backends** – AWS EBS, Azure Disk.
     - **Access modes define how PVs are used** – ReadWriteOnce, ReadOnlyMany, ReadWriteMany.

#### PVC - Persistent Volume Claims
- They are requests for storage by pods
- they are like request for storage by a Pod in Kubernetes. It allows applications to use persistent storage that remains intact even if a Pod is restarted or rescheduled.
     - A PVC requests storage from a Persistent Volume (PV).
     - Ensures data persistence across Pod restarts and rescheduling.
     - Works with different storage backends ( EBS, NFS, Azure Disk ).
     - Uses Storage Classes for dynamic volume provisioning.

#### Operator
- An operator is an automated way to manage complex applications using custom resources.
- It combines two key components:
     - **Controller**: A controller continuously monitors the state of resources and ensures the actual state matches the desired configuration. When something changes or fails, the controller works to bring things back into alignment.
     - **Custom Resources (CRs)**: These are user-defined extensions to the Kubernetes API, allowing new functionality not provided by default. Custom Resource Definitions (CRDs) let you define new resources to represent your app's needs.
![image](https://github.com/user-attachments/assets/732ca853-8ffb-4d94-9c08-42cd56991e6c)


#### YAML – Deployment with Environment Variable - Kubernetes

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          env:
            - name: ENVIRONMENT
              value: "production"
```

- Explanation:
     - Deployment: Ensures specified number of pods are running.
     - replicas: 3: Run 3 pods.
     - matchLabels: Matches pods to this deployment.
     - template: Pod definition inside deployment.
     - env: Sets an environment variable inside the container.
- Contents 
     - **apiVersion** and kind define the resource type
     - **metadata** specify the details of deployment such as the name of the deployment, labels.
     - **spec** defines the desired state of the Deployment.
     - **replicas** specify the desired number of pods to maintain.
     - **selector** - specifies on which pod the replica configuration should be applied with the help of the label of the pod.
     - **template** describes the pod template and how deployment will use it to create new pods.
     - **containers** will list the containers to run within the pod.
     - **name** specifies the name of the container
     - **image** specifies the name that will be used to run the container. The image will be a Docker Image.
     - **containerport** specifies the port at which the container will listen for incoming traffic.
Use Case: **PostgreSQL Database Operator**
- Imagine setting up a highly available PostgreSQL database. With an Operator, you can automate scaling, backup, and failover with just a few configuration settings, without manually configuring these tasks every time.

```
apiVersion: postgresql.dev/v1
kind: PostgresCluster
metadata:
name: techops-database
spec:
instances: 3
backups:
enable: true
schedule: "0 3 * * *" # Daily backups at 3 am
storage:
size: 100Gi
```

#### Kubernetes errors and fix
#### CrashLoopBackOff in a Production Pod
-  A critical application pod was stuck in a CrashLoopBackOff state. Logs showed an OutOfMemoryError.
- The container repeatedly crashes after starting.
     - Application misconfiguration
     - Missing dependencies
     - Insufficient resources (CPU/Memory)
     - Improper readiness/liveness probes
     - The container exits immediately after execution
          - kubectl logs <pod-name> -n <namespace>
          - kubectl describe pod <pod-name> -n <namespace>
          - Increase CPU/memory limits in the pod spec..
          - Ensure application handles errors properly.
     - Optimized JVM heap size (for Java app).
     - Deployed with a resource monitoring policy.

#### **ImagePullBackOff / ErrImagePull** 
- Issue: New deployment failed with ImagePullBackOff due to missing authentication credentials for a private container registry.
- when Kubernetes cannot pull the container image from the registry.
     - Incorrect image name or tag
     - Authentication issues with private registry
     - Image does not exist in the registry
          - Verify the image name and tag
          - If using a private registry, ensure correct credentials:
          - Restart pod after fixing the issue.
     - Verified registry access (kubectl get secrets).
     - Recreated the imagePullSecret and added it to the service account.
     - Updated deployment YAML to reference the correct secret.
     - Used kubectl describe pod to confirm successful pull.
       
#### OOMKilled (Out of Memory)
- The container exceeds its allocated memory limit.
     -  Insufficient memory allocated in the pod spec.
     -  A memory leak in the application.
          - Increase memory limits in the pod YAML
          - kubectl top pod <pod-name> -n <namespace>

#### NodeNotReady
- The node is not in a ready state.
     - Node running out of resources
     - Network connectivity issues
     - Kubernetes components (kubelet, etc.) not running properly
          - kubectl get nodes
          - kubectl describe node <node-name>
          - systemctl restart kubelet
          - journalctl -u kubelet -f
            
#### PodPending
- The pod is stuck in a pending state and is not getting scheduled.
     - Insufficient resources in the cluster
     - NodeSelector, Taints, or Affinity rules preventing scheduling
     - PVC (Persistent Volume Claim) is not bound
          -  kubectl describe pod <pod-name>
          -  kubectl describe nodes
          -  kubectl get pvc
            
#### CreateContainerConfigError
- The container cannot be created due to incorrect configurations.
     - Incorrect environment variables
     - Secret or ConfigMap missing
     - Volume mount issues
          -  kubectl describe pod <pod-name>
          - kubectl get configmap
          - kubectl get secret
            
#### CreateContainerError
- Kubernetes fails to start the container.
     - Problems with the container runtime
     - Insufficient privileges
     - Host path issues
          - journalctl -u containerd -f

#### DeadlineExceeded
- A job or request takes too long and gets terminated.
     - Timeout in readiness/liveness probes
     - Network issues preventing completion
          - kubectl get svc

#### BackOff
- Kubernetes retries starting a container but fails repeatedly.
     - Failed dependencies
     - Readiness probe failures
          - kubectl get events --sort-by=.metadata.creationTimestamp
            
#### Unauthorized / Forbidden Errors
- Permissions issue with Kubernetes RBAC. 
     - ServiceAccount lacks required roles
     - Missing ClusterRole or Role bindings
          - kubectl auth can-i <action> <resource>
          - kubectl get rolebinding -A

#### PersistentVolumeClaim (PVC) Stuck in Pending
- Issue: An application required persistent storage, but the PVC was stuck in Pending.
     - Checked storage class (kubectl get storageclass).
     - Found that no available PersistentVolumes matched the PVC.
     - Created a new PersistentVolume and bound it manually.
     - Automated PVC creation via Helm to avoid misconfigurations.

#### High Latency in Services Due to Improper HPA
- Issue: A microservice was experiencing high response times under load, even though Horizontal Pod Autoscaler (HPA) was enabled.
     - Investigated using kubectl top pods and kubectl describe hpa.
     - Found that CPU-based scaling was configured, but the application was memory-intensive.
     - Changed HPA to scale based on memory and custom application metrics.
     - Improved readinessProbe to remove unresponsive pods from load balancer rotation.

#### Kubernetes Cluster Node Failure (Production Incident)
- Issue: One of the worker nodes in the cluster went NotReady, causing some workloads to become unavailable.
     - Used kubectl get nodes and kubectl describe node to check node status.
     - Found an issue with disk space (Filesystem full).
     - Cleaned up unused Docker images and logs.
     - Restarted the kubelet service (systemctl restart kubelet).
     - Applied auto-recovery automation via node draining and re-provisioning.

#### Network Latency Between Services (Intermittent 5XX Errors)
- Issue: Microservices communication was unstable, leading to intermittent 502 and 504 errors.
     - Used kubectl exec to test connectivity and kubectl logs for debugging.
     - Found that istio sidecar injection was missing for some pods.
     - Re-deployed affected services with Istio sidecar enabled.
     - Implemented a retry policy at the Istio virtual service level.

#### Kubernetes Ingress Controller Misconfiguration
- Issue: A new service was not accessible externally, and Ingress showed 404 Not Found.
     - Used kubectl describe ingress to check misconfiguration.
     - Found missing host field in ingress rules.
     - Updated the Ingress YAML with the correct path and host.
     - Restarted the Nginx Ingress Controller pod.

#### Secrets Management Issue (Application Failing to Start)
- Issue: An application failed to start due to missing environment variables that were sourced from Kubernetes Secrets.
     - Verified secrets using kubectl get secrets and kubectl describe secrets.
     - Found incorrect reference in deployment YAML.
     - Updated the deployment to use envFrom instead of manually specifying each variable.
     - Enabled automatic secret rotation via an external secrets manager.

#### HPA (Horizontal Pod Autoscaler)  & VPA ( Vertical Pod Autoscaler )
- **HPA (Horizontal Pod Autoscaler)** : when we set it automatically scale the number of pods in a deployment, replica set, or stateful set based on observed CPU/memory utilization or custom metrics.
- **VPA ( Vertical Pod Autoscaler )** : its a component that monitors resource consumption and automatically adjusts pod CPU and memory requests.
- Unlike Horizontal Pod Autoscaler (HPA), which scales the number of pods, VPA scales up or down the resources assigned to each pod

**HPA in detail :**
1. **Benifits:**
     - **Handles Dynamic Traffic** – Automatically adjusts pod count based on real-time demand.
     - **Improves Application Availability** – Prevents performance degradation during high loads.
     - **Optimizes Resource Costs** – Scales down pods when demand is low to save resources.
     - **Works with Custom Metrics** – Can scale pods based on application-specific metrics (e.g., request latency).
2. **How Does HPA Work**
     - HPA continuously monitors metrics and adjusts the number of pods accordingly:
     - **Metrics Collection** – HPA uses the Kubernetes Metrics Server or external/custom metrics to evaluate CPU, memory, or other parameters.
     - **Scaling Decision** – If resource usage exceeds the defined threshold, HPA adds more pods; if usage drops, it removes pods.
     - **Pod Scaling** – Kubernetes adjusts the number of running pods in the deployment.
3. **Best Practices for HPA**
     - Use with VPA – Combine HPA for pod scaling and VPA for resource tuning.
     - Define Proper Min/Max Limits – Prevent excessive scaling that may affect performance or cost.
     - Monitor Scaling Behavior – Use monitoring tools like Prometheus + Grafana.
     - Use Custom Metrics – Scale based on real-world application load (e.g., request count).
4. **When to Use HPA**
     - Web applications with fluctuating traffic.
     - Microservices that need dynamic scaling.
     - Event-driven applications where load varies based on external triggers.

**VPA in detail :**
1. **Benifits:**
     - **Optimized Resource Usage:** Ensures applications get only the necessary resources, reducing underutilization.
     - **Improved Performance:** Prevents CPU throttling and Out of Memory (OOM) issues by assigning appropriate resources.
     - **Reduced Manual Effort:** Automates resource allocation, reducing the need for manual tuning.
     - **Better Cost Management:** Prevents over-provisioning and minimizes cloud resource costs.
2. **How Does VPA Work**
- VPA operates in three main modes:
     - **Off Mode** – Only provides recommendations without making actual changes.
     - **Auto Mode** – Automatically updates pod resource requests.
     - **Initial Mode** – Sets resource requests only at pod startup.
3. **VPA Components**
     - **VPA Recommender**: Analyzes pod resource usage and suggests CPU/memory adjustments.
     - **VPA Updater:** Reschedules pods with updated resource limits.
     - **VPA Admission Controller:** Adjusts resource requests for newly created pods.
4. **Best Practices for Using VPA**
     - **Use VPA with HPA:** Combine VPA for resource tuning and HPA for scaling pods.
     - **Enable VPA in "Off" Mode First:** Start with recommendations before enabling auto mode.
     - **Monitor Pod Restart Impact:** Since VPA may restart pods, be cautious with stateful applications.
     - **Use Custom Metrics:** Fine-tune scaling policies based on real-world application behavior.
5. **When to Use VPA**
     - For stateful applications (databases, caching services) that need controlled resource scaling.
     - When optimizing CPU and memory allocation for cost efficiency.
     - In microservices environments, where resource usage varies across services.

#### K8s States and Components (kubectl get all) kubernetes
- When you run kubectl get all, you get an overview of various Kubernetes resources in the current namespace, including:
- **Core Components Displayed by kubectl get all**

| Component	| Description |
| ---------- |----------------------------------------- |
|Pod	| The smallest deployable unit in Kubernetes that runs a container or multiple containers. |
|Deployment	| Manages a set of identical pods, ensuring high availability and rolling updates. |
|ReplicaSet	| Ensures a specified number of pod replicas are running at all times. |
|StatefulSet |	Manages stateful applications, providing stable network IDs and persistent storage. |
|DaemonSet	| Ensures a copy of a pod runs on each (or some) nodes (e.g., logging or monitoring agents). |
|Service	| Exposes a set of pods as a network service. |
|Ingress	| Manages external access to services, usually via HTTP/HTTPS. |
|Job	| Runs a task to completion, ensuring it runs a specified number of times. |
|CronJob |	Runs scheduled jobs at specified intervals. |

- **Key Fields in kubectl get all Output** - General Resource Fields

|Field	| Description |
| ---------- |----------------------------------------- |
|Name	| The name of the resource (Pod, Deployment, Service, etc.).|
|Type	| The type of resource (e.g., ClusterIP, NodePort, LoadBalancer for services).|
|Cluster-IP |	Internal IP of a service within the cluster (if None, it means it's a headless service). |
|External-IP	| IP accessible from outside the cluster (only for LoadBalancer or NodePort services).|
|Ports |	Ports exposed by the service or pod (e.g., 9093, 9094, 8080, etc.).|
|Age	| The duration since the resource was created. |

 - **Deployment & ReplicaSet Specific Fields**

|Field	| Description |
| ---------- |----------------------------------------- |
|Desired |	Number of replicas expected in a Deployment or ReplicaSet.|
|Current	| Number of replicas currently scheduled.|
|Ready	| Number of replicas that are successfully running and ready to serve traffic. |
|Up-to-Date |	Number of replicas updated to the latest version.|
|Available |	Number of replicas available to serve traffic.|

#### Ingress
- Ingress Architecture
     - Ingress Controller: Handles external traffic (e.g., Nginx, Traefik)
     - Ingress Resource: Defines routing rules (ingress.yaml)
     - Service: Exposes your application inside Kubernetes
     - Pods: Run your application
- **Ingress Controller **
- its a component in Kubernetes that manages external access to services inside a cluster, typically via HTTP/HTTPS. It routes requests based on hostnames, paths, or other rules
     - Single Entry Point – Manages all external traffic
     - Path-Based Routing – Routes /api to one service, /web to another
     - SSL Termination – Manages HTTPS for all services
     - Load Balancing – Distributes traffic between pods
     - Authentication & Security – Enables OAuth, JWT, and IP whitelisting

#### Step-by-Step Process for Kubernetes Cluster Deployment

- We need to 
     - Automate everything using Terraform, Ansible, Helm, ArgoCD.
     - Ensure security with IAM, RBAC, Secrets Management, Network Policies.
     - Optimize observability with Prometheus, Grafana, ELK, Datadog.
     - Improve resilience with Chaos Engineering, Backup Strategies, Disaster Recovery.

1. Planning & Architecture Design
     - Assessed business requirements (e.g., high availability, security, scaling needs).
     - Chose Cloud Provider (AWS EKS, Azure AKS, or on-premises with Kubernetes).
     - Defined Cluster Topology:
          - Master & Worker Nodes placement.
          - Node Pools for different workloads (e.g., General-purpose, GPU, Spot instances).
          - Network Design (VPC, Subnets, CIDR blocks, Security Groups)
      
2. Infrastructure Provisioning (IaC)
     - Used Terraform & Ansible to automate infrastructure deployment:
          - Terraform for provisioning cloud resources (VPC, Subnets, EKS/AKS Cluster, IAM roles, Storage).
          - Ansible for configuring nodes, setting up monitoring agents, and applying security hardening.
- Example Terraform Code for AWS EKS Deployment
```
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  cluster_name    = "prod-cluster"
  cluster_version = "1.26"
  subnets         = module.vpc.private_subnets
  vpc_id          = module.vpc.vpc_id

  node_groups = {
    worker_nodes = {
      desired_capacity = 3
      max_capacity     = 5
      min_capacity     = 2
      instance_types   = ["t3.medium"]
    }
  }
}
```

3. Cluster Bootstrapping & Configuration
     - Deploying Kubernetes Control Plane & Worker Nodes:
          - Used eksctl for AWS or az aks create for Azure.
          - Set up RBAC & IAM roles for secure access control.
          - Configured Kubeconfig to connect the cluster.
          - Enabled Cluster Autoscaler & Horizontal Pod Autoscaler (HPA) for auto-scaling.
     - Example Command for EKS Cluster Creation:
          - eksctl create cluster --name prod-cluster --region us-east-1 --nodegroup-name worker-nodes --nodes 3
          - az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 3 --enable-managed-identity

4. Networking & Security Setup
     - Configured Ingress Controller (NGINX/Istio) for external traffic routing.
     - Set up Network Policies to restrict pod-to-pod communication.
     - Integrated Cloud Security Best Practices:
          - IAM Roles for Service Accounts (IRSA) for pod security.
          - AWS Security Groups/NACLs for network-level protection.
          - Secrets Management with AWS Secrets Manager/HashiCorp Vault.
- Example Network Policy to Restrict Traffic
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-ingress
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: allowed-app
```

5. Deploying Applications & Services
     - Used Helm Charts for standardized app deployment.
     - ArgoCD/GitOps for continuous delivery.
     - Canary & Blue-Green Deployment for zero-downtime releases.
- Helm Command to Deploy an Application:
```
# Helm Command to Deploy an Application
helm install my-app ./charts/my-app
----
# ArgoCD Deployment Manifest Example
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    repoURL: 'https://github.com/my-org/my-app.git'
    path: charts/my-app
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: my-namespace
```

6. Observability & Monitoring Setup
     - Installed Prometheus & Grafana for metrics.
     - Set up ELK/Splunk for log aggregation.
     - Configured Datadog/New Relic for APM (Application Performance Monitoring).
     - Integrated Alerting via Slack/Teams using Alertmanager.
- Example Prometheus Alert Rule for High CPU Usage:
```
groups:
- name: High CPU Usage Alert
  rules:
  - alert: HighCPUUsage
    expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
    for: 2m
    labels:
      severity: critical
    annotations:
      summary: "High CPU usage on instance {{ $labels.instance }}"
```

7. Scaling, Security & Disaster Recovery
     - Configured Cluster Autoscaler & VPA (Vertical Pod Autoscaler).
     - Set up Pod Disruption Budgets (PDBs) to ensure high availability.
     - Configured AWS Backup & EFS snapshots for disaster recovery.
     - Conducted Chaos Engineering (using LitmusChaos) to test system resilience.
- Example Pod Disruption Budget Configuration:
```
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

8. Incident Management & Continuous Improvements
     - Integrated SLO/SLI/SLA tracking.
     - Set up blameless postmortems and RCAs for production issues.
     - Conducted weekly optimizations & security audits to enhance performance.
- Example SLI/SLO Definition in Prometheus:
```
- record: slo:availability
  expr: sum(rate(http_requests_total{status=~"2.."}[5m])) / sum(rate(http_requests_total[5m])) * 100
```

- Final Outcome & Impact
     - Deployment time reduced from 2 hours to 15 minutes using Terraform & CI/CD.
     - MTTR (Mean Time to Recovery) reduced by 40% with proactive monitoring.
     - Cloud cost optimized by 30% using auto-scaling & Spot instances.
     - System availability improved to 99.99% with robust DR & HA strategies.

#### SLO, SLA, SLI & Error Budget
1. **SLA (Service Level Agreement)** - its a business-level commitments based on SLOs, often with penalties. **External commitment (contract between provider & customer).**
   - Example: "99.95% uptime per month, or a 5% refund."
   - Use: Ensures accountability; failing an SLA can have financial/legal consequences.
2. **SLO (Service Level Objective)** - A target value for an SLI that defines acceptable performance.  **Internal goal (target reliability, e.g., 99.99% uptime).**
   - Example: "99.9% uptime over 30 days" or "p99 latency < 500ms".
   - Use: Sets a goal for reliability and user experience.
3. **SLI (Service Level Indicator)** - A quantitative measure of a system's performance. - the raw metrics - **Measured metric (actual 99.97% uptime recorded).**
   - Example: Response time, uptime percentage, error rate.
   - Use: Helps track how well a service is performing.
4. **Error Budget** - The allowable margin of failure within an SLO.
   - Example: If the SLO is 99.9% uptime, the error budget is 0.1% downtime (about 43.8 minutes per month).
   - Use: Helps balance innovation and reliability.
   - Error budget defines the acceptable level of failure before SLO violations.
![image](https://github.com/user-attachments/assets/7b93fc84-a951-417f-8190-2863db298798)

![image](https://github.com/user-attachments/assets/44042a63-ef17-43e9-96d7-877fdd1d7b7c)

**Metrics :**
1. Uptime/Availability (e.g., 99.9% availability)
2. Response Time (e.g., incidents responded to within 15 minutes)
3. Resolution Time (e.g., critical issues fixed within 4 hours)
4. Performance Metrics (e.g., API response time < 200ms)
5. Error Rates (e.g., < 0.01% failure rate)
6. Support & Escalation Process

#### API
An API (**Application Programming Interface**) is a set of rules and protocols that allows different software applications to communicate with each other. It defines how requests and responses should be structured, enabling seamless interaction between systems, applications, or services.
1. **General Explanation**
     - Facilitates communication between different software systems.
     - Enables modular and scalable development.
     - Promotes reusability of functionalities.
2. **For Developers (Backend, Frontend, Full Stack, Mobile, etc.)**
     - Backend Dev: Helps expose business logic securely to frontend applications.
     - Frontend Dev: Fetches and displays data dynamically using APIs.
     - Mobile Dev: Allows mobile apps to interact with cloud-based services.
3. **For System Architects & DevOps**
     - Helps integrate microservices for scalable applications.
     - Ensures interoperability between different platforms (e.g., cloud services).
4. **For Data Engineers & AI Engineers**
     - Enables fetching and sending large-scale data efficiently.
     - Provides access to external AI models and services.
- **Stages in API Gateway Deployment**
     - When creating an API Gateway (AWS API Gateway, Kong, Apigee, or any other API management tool), multiple stages allow for controlled deployment, testing, and versioning of APIs.
- **Common Stages in API Gateway**
     - **dev**	Development stage (used by developers, may have debugging enabled)
     - **test**	Used for API testing (integration and unit tests)
     - **staging (pre-prod)**	Pre-production stage, mirrors production for final validation
     - **prod**	Live production API, used by real customers
     - **beta**	Used for limited user testing before full production release
- **How SRE & DevOps Use API Gateway Stages**
     - **Traffic Routing** → Different environments for controlled API releases
     - **Rate Limiting & Throttling** → More relaxed in dev, strict in prod
     - **Logging & Monitoring** → More detailed logs in test/dev, essential metrics in prod
     - **Feature Flags** → Enable/disable API features per stage
     - **Rollback Mechanisms** → Deploy a stable previous version in case of issues
- **Canary Deployment & Blue-Green Deployment**
     - **Canary Release** → Gradually roll out new API versions to a small % of users before full deployment.
     - **Blue-Green Deployment** → Switch traffic between old (blue) and new (green) versions instantly.

#### REST API
**Representational State Transfer Application Programming Interface**
- its a web service which follows RESTful principles, it enables communication between client and server over HTTP.
     - **Stateless:** Each request is independent and does not store client session data.
     - **Client-Server Architecture:** Separation of concerns between UI (client) and backend (server).
     - **Cacheable:** Responses can be cached to improve performance.
     - **Uniform Interface:** Uses standard HTTP methods (GET, POST, PUT, DELETE).
     - **Resource-Based:** Data is represented as resources (e.g., /users, /orders).

#### RESTful API (Representational State Transfer API) 
- its a web service architecture style that allows systems to communicate over HTTP using simple, stateless operations. It's widely used in modern applications, especially in microservices and web development.
- Used in DevOps/SRE
     - REST APIs for cloud platforms (AWS, Azure)
     - Monitoring tools like Prometheus or Grafana expose APIs
     - CI/CD tools (like Jenkins, GitLab) expose APIs for triggering jobs
     - Terraform Cloud and ArgoCD also expose RESTful APIs for automation

#### API Key
- its an unique identifier used to authenticate requests made to an API. It helps control access and track API usage.
     - Client sends a request with an API key in the header, URL, or request body.
     - API server validates the key against a database or authentication system.
     - If valid, the request is processed; otherwise, it's rejected.
- use cases
     - **Authentication & Access Control** – Verify who is making API requests.
     - Rate Limiting & Throttling – Prevent abuse by restricting request frequency.
     - Monitoring & Logging – Track API usage for security and billing.
     - Integrations & Automation – Used in CI/CD pipelines, monitoring tools, and cloud services

#### API access
- details
     - it refers to how services, users, and applications interact with APIs securely and efficiently.
     - we need to manage API authentication, authorization, security, and monitoring.
     - secure API access in a DevOps environment
          - Authentication – Use OAuth 2.0, JWT, API keys, AWS IAM Roles to verify identity.
          - Authorization – Implement RBAC (Role-Based Access Control) or ABAC (Attribute-Based Access Control).
          - Rate Limiting & Throttling – Prevent abuse using tools like AWS API Gateway, Kong, or Nginx.
          - Encryption – Use TLS/SSL for securing data in transit.
          - Logging & Monitoring – Track API requests via CloudWatch, Prometheus, ELK Stack, or Datadog.

#### API Gateway
- info
     - its a central entry point for managing, securing, and routing API requests between clients and backend services.
     - It acts as a reverse proxy that controls traffic, performs authentication, and enforces security policies.

#### TLS (Transport Layer Security) and SSL (Secure Sockets Layer) 
- Content
     - TLS (Transport Layer Security) is a cryptographic protocol that secures data transmission between systems over the internet.
     - It ensures confidentiality, integrity, and authentication of data, preventing eavesdropping, tampering, and impersonation.
- TLS ensures:
     - Confidentiality (Data is encrypted and cannot be read by unauthorized parties).
     - Integrity (Data is not altered during transmission).
     - Authentication (Validates the identity of the communicating parties).
- SSL (Secure Sockets Layer) is a cryptographic protocol designed to secure communication over the internet by encrypting data between a client (e.g., browser, API, microservice) and a server
- It helps protect sensitive information from hackers, ensuring privacy, integrity, and authentication.

- SSL/TLS Used (Real-World Use Cases)
     - Websites & APIs → HTTPS ensures secure web traffic (NGINX, Apache, AWS Load Balancer, API Gateway).
     - Kubernetes & Microservices → Mutual TLS (mTLS) secures communication (Istio, Linkerd).
     - Cloud Security → AWS, Azure, and GCP enforce TLS for data encryption.
     - DevOps Pipelines → TLS secures GitHub, Jenkins, CI/CD data transfers.
     - Database Encryption → TLS protects connections in MySQL, PostgreSQL, MongoDB.

#### API Server (kube-apiserver)
- The API Server is the central control plane component in Kubernetes, acting as the gateway for all interactions with the cluster. It validates, processes, and serves API requests from users, controllers, and other components.
- **Key Responsibilities of API Server**
- Handles All API Requests
     - The API server exposes the Kubernetes API over HTTP(S) and serves as the frontend for the control plane.
     - All interactions with the cluster (e.g., kubectl, UI, automation) go through the API server.
- Authentication & Authorization
     - It enforces authentication (Token, OAuth, Client Certificates, etc.).
     - Implements authorization mechanisms like RBAC, ABAC, and Webhook authorizers.
- Validates & Processes Requests
     - Ensures API requests are well-formed and conform to Kubernetes schema (using OpenAPI).
     - Performs request validation before persisting data.
- Acts as the Gatekeeper to etcd
     - Persists and retrieves cluster state from etcd, ensuring strong consistency.
     - The API server is the only component that directly communicates with etcd.
- Admission Control & Policy Enforcement
     - Uses admission controllers to enforce security, resource limits, and best practices.
     - Examples: Namespace limits, Pod Security Admission, ResourceQuota.
- Handles API Aggregation & Extensions
     - Supports Custom Resource Definitions (CRDs) and API Aggregation Layer to extend Kubernetes APIs

#### Application Gateway

- its one of the most powerful Layer 7 load balancers in Azure, especially when combined with AKS for secure ingress traffic.
- It provides SSL termination, URL-based routing, WAF, and host-based routing, making it a go-to choice for secure, scalable applications.

#### Alias for DevOps Engineer

```
alias k='kubectl'
alias ke='kubectl exec -it'
alias kx='kubectl exec -it -- /bin/sh'
alias kd='kubectl describe'
alias krm='kubectl delete'
alias kga='kubectl get all'
alias kdp='kubectl describe pod'
alias kds='kubectl describe svc'
alias kdr='kubectl describe rs'
alias kdn='kubectl describe node'
alias kdg='kubectl describe deployment'
alias kgp='kubectl get pods'
alias kgs='kubectl get svc'
alias kns='kubectl config set-context --current-context --namespace'
alias kaf='kubectl apply -f'
alias kdf='kubectl delete -f'
alias kcg='kubectl config get-clusters'
alias krun='kubectl run'
alias kcp='kubectl cp'
alias kctx='kubectl config current-context'
alias kxsh='kubectl exec -it -- /bin/sh'
alias klog='kubectl logs -f'
alias ktop='kubectl top'
alias kxsh='kubectl exec -it -- /bin/sh'
alias ktopn='kubectl top nodes'
alias ktopc='kubectl top pods'
alias kwatch='kubectl get pods --watch'
alias kedit='kubectl edit'
alias kxbash='kubectl exec -it -- /bin/bash'
alias kget='kubectl get'
alias kgetp='kubectl get pods'
alias kgetd='kubectl get deployments'
alias kgetn='kubectl get nodes'
alias kgetsvc='kubectl get svc'
alias kgetrs='kubectl get rs'

```
