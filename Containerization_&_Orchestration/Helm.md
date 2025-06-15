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
```helm-chart/
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
