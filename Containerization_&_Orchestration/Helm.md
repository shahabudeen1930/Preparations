#### Helm  & Helm Charts
- Helm : pre-configured Kubernetes resources which helps manage Kubernetes applications
- **Components** :
     - Chart – A collection of YAML templates defining Kubernetes resources.
     - Release – A deployed instance of a Helm chart.
     - Repository – A collection of charts stored in a remote or local locatio
- **Helm chart** - its a package of Kubernetes YAML templates that define how an application is deployed
- **helm chart structure** 
- those are templated Kubernetes manifests packaged together for easy deployment, versioning, and management of applications in Kubernetes.
- They help automate complex deployments, making application deployment repeatable, scalable, and maintainable.
```
helm-chart/
│── charts/                 # Dependency charts (if any)
│── templates/              # Kubernetes manifests with templating
│   ├── deployment.yaml     # Defines Deployment resource
│   ├── service.yaml        # Defines Service resource
│   ├── ingress.yaml        # Defines Ingress resource
│   ├── _helpers.tpl        # Contains reusable template functions
│   ├── hpa.yaml            # Defines Horizontal Pod Autoscaler (optional)
│   ├── configmap.yaml      # ConfigMap resource
│   ├── secret.yaml         # Secret resource
│── values.yaml             # Default configuration values
│── Chart.yaml              # Chart metadata (name, version, dependencies)
│── README.md               # Documentation for the chart
```
- Specific Experience Areas
1. Helm Chart Management
     - Developed and customized Helm charts for deploying microservices-based applications.
     - Used Chart.yaml and values.yaml to parameterize configurations.
     - Managed Helm dependencies using requirements.yaml or Chart.yaml.
2. Helm in CI/CD Pipelines
     - Integrated Helm with Jenkins, GitHub Actions, and ArgoCD to automate deployments.
     - Implemented GitOps workflows using Helm + ArgoCD for declarative application management.
     - Used Helmfile to manage multiple Helm charts across environments.
3. Helm Upgrades and Rollbacks
     - Used helm upgrade --install for seamless application upgrades.
     - Managed rollbacks using helm rollback in case of failed deployments.
     - Implemented Helm hooks for pre- and post-deployment tasks.
5. Helm and Kubernetes Security
     - Used Helm Secrets and sealed-secrets for managing sensitive configurations.
     - Applied RBAC policies to restrict Helm releases to specific namespaces.
     - Scanned Helm charts for security vulnerabilities using Trivy and Checkov.
6. Helm for Multi-Tenant Kubernetes Clusters
     - Managed multi-tenant Kubernetes environments with Helm namespaces and releases.
     - Implemented Helm templating for reusable and scalable Kubernetes configurations.

#### Helm Chart architecture
- A Helm chart is a package format that bundles Kubernetes resources to deploy applications in a repeatable manner.
- It provides all necessary configurations, dependencies, and templates for setting up applications in a Kubernetes cluster, acting as a blueprint for reusable, customizable deployments.

### Example of helm and helm chart 
- A simple Helm chart for a DevOps task — deploying a basic Nginx web server with ConfigMap, Service, and customizable replicas. This demonstrates Helm’s templating, values management, and Kubernetes object definitions.

**1. Folder Structure**
```
my-nginx-chart/
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default configuration
├── templates/          # Kubernetes manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   └── _helpers.tpl    # Helper templates (optional)

```
**2. Chart.yaml**
```
apiVersion: v2
name: my-nginx-chart
description: A simple Helm chart for Nginx (DevOps example)
version: 0.1.0
appVersion: "1.25.3"  # Nginx version
```
**3. values.yaml**
```
replicaCount: 2
image:
  repository: nginx
  tag: alpine
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
config:
  nginxConf: |
    server {
      listen 80;
      location / {
        return 200 "Hello from Nginx (DevOps Helm Example)!\n";
      }
    }
```
**4. templates/deployment.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Chart.Name }}-deployment
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nginx-config
          mountPath: /etc/nginx/conf.d/default.conf
          subPath: default.conf
      volumes:
      - name: nginx-config
        configMap:
          name: {{ .Chart.Name }}-config
```
**5. templates/service.yaml**
```
apiVersion: v1
kind: Service
metadata:
  name: {{ .Chart.Name }}-service
spec:
  type: {{ .Values.service.type }}
  ports:
  - port: {{ .Values.service.port }}
    targetPort: 80
  selector:
    app: {{ .Chart.Name }}
```
**6. templates/configmap.yaml**
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Chart.Name }}-config
data:
  default.conf: |
    {{ .Values.config.nginxConf | indent 4 }}
```
**7. Deploy the Chart**
```
# Install the chart
helm install my-nginx ./my-nginx-chart

# Upgrade with custom values (e.g., scale to 3 replicas)
helm upgrade my-nginx ./my-nginx-chart --set replicaCount=3

# Verify
kubectl get pods,svc,configmap
```
