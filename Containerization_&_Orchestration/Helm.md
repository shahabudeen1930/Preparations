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
**8. templates/ingress.yaml**
               - Add Ingress (templates/ingress.yaml) for external access.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ .Chart.Name }}-ingress
  annotations:
    # Example annotations (adjust for your Ingress Controller)
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx"  # Use "alb" for AWS ALB, etc.
spec:
  rules:
  - host: {{ .Values.ingress.host | default "nginx.example.com" }}  # Override in values.yaml or with --set
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: {{ .Chart.Name }}-service  # Matches the Service name in service.yaml
            port:
              number: {{ .Values.service.port }}  # Matches service.port from values.yaml
```
- Key Notes:
     - Annotations: Customize for your Ingress Controller (e.g., alb.ingress.kubernetes.io/scheme: internet-facing for AWS ALB).
     - TLS: Add a tls section to the Ingress spec for HTTPS (requires a cert-manager or pre-provisioned certificate).
     - Hosts: Ensure DNS points to your Ingress Controller’s external IP/LB.

#### Helm Hook Types

   - Hooks are defined using annotations and run at these stages:
     - pre-install, post-install
     - pre-upgrade, post-upgrade
     - pre-rollback, post-rollback
     - pre-delete, post-delete
     - test (for test suites)

**Folder Structure**
```
my-chart/
├── templates/
│   ├── pre-install-job.yaml
│   ├── post-install-job.yaml
│   ├── pre-delete-job.yaml
│   └── test-pod.yaml
```

**1. File: templates/pre-install-job.yaml**
     - Example: pre-install Job (Validate Dependencies)
```
apiVersion: batch/v1
kind: Job
metadata:
  name: "{{ .Chart.Name }}-pre-install-check"
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-weight": "-5"  # Lower weights run first
    "helm.sh/hook-delete-policy": hook-succeeded  # Delete the pod after success
spec:
  template:
    spec:
      containers:
      - name: pre-install-check
        image: alpine
        command: ["/bin/sh", "-c"]
        args:
          - echo "Checking cluster dependencies...";
            kubectl get ns {{ .Values.namespace }} || exit 1;
            echo "Pre-install validation passed!";
      restartPolicy: Never
```

**2. File: templates/post-install-job.yaml**
     - Example: post-install Notification (Slack Webhook)
```
apiVersion: batch/v1
kind: Job
metadata:
  name: "{{ .Chart.Name }}-post-install-notify"
  annotations:
    "helm.sh/hook": post-install
    "helm.sh/hook-weight": "5"  # Higher weights run later
spec:
  template:
    spec:
      containers:
      - name: slack-notify
        image: curlimages/curl
        command: ["/bin/sh", "-c"]
        args:
          - 'curl -X POST -H "Content-type: application/json" \
            --data "{\"text\":\"🚀 Helm release {{ .Release.Name }} deployed to {{ .Release.Namespace }}\"}" \
            {{ .Values.slack.webhookUrl }}'
      restartPolicy: Never
```
**Add to values.yaml:**
```
slack:
  webhookUrl: "https://hooks.slack.com/services/XXXX/YYYY/ZZZZ"
```

**3. File: templates/pre-delete-job.yaml**
     - Example: pre-delete Cleanup (Delete PVCs)
```
apiVersion: batch/v1
kind: Job
metadata:
  name: "{{ .Chart.Name }}-pre-delete-cleanup"
  annotations:
    "helm.sh/hook": pre-delete
    "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
spec:
  template:
    spec:
      containers:
      - name: cleanup-pvcs
        image: bitnami/kubectl
        command: ["/bin/sh", "-c"]
        args:
          - kubectl delete pvc -l app.kubernetes.io/instance={{ .Release.Name }}
      restartPolicy: Never
```

**4. File: templates/test-pod.yaml**
     - Example: test Hook (Smoke Test)
```
apiVersion: v1
kind: Pod
metadata:
  name: "{{ .Chart.Name }}-test-connection"
  annotations:
    "helm.sh/hook": test
spec:
  containers:
  - name: curl-test
    image: curlimages/curl
    command: ["curl", "http://{{ .Chart.Name }}-service:{{ .Values.service.port }}"]
  restartPolicy: Never
```
**Run with tests**
```
helm test <RELEASE_NAME>
```

#### Key DevOps Concepts Demonstrated
- Templating: Uses {{ .Values.* }} for dynamic configs (replicas, image tags, etc.).
- Config Management: ConfigMap injects Nginx configuration.
- Scalability: replicaCount can be adjusted during deployment.
- Reusability: Override defaults via --set or custom values.yaml.

#### Customization Tips:
- Add Ingress (templates/ingress.yaml) for external access.
- Use Helm Hooks (e.g., post-install) for cleanup/notifications.
- Store secrets securely with Helm Secrets or external vaults.

### When to Use Hooks
- Pre-install: Validate cluster state.
- Post-install: Notifications, backups, or seeding databases.
- Pre-delete: Clean up resources Helm can’t manage (e.g., PVs).
- Tests: Validate deployments.


